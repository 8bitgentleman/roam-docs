# Extension Analysis: Better Bullets

This document analyzes the [Better Bullets](https://github.com/mlava/better-bullets) Roam Research extension to understand how it uses block props and CSS to create custom bullet icons.

## Overview

Better Bullets replaces Roam's default bullet dots with meaningful glyphs (like `→`, `⇒`, `?`, `!`) triggered by typing prefix markers at the start of a block. For example, typing `-> ` at the beginning of a block changes the bullet to an arrow (`→`).

## How It Works: The Three-Step Process

### 1. Prefix Detection (JavaScript)

When you type in Roam, the extension watches for changes using `roamAlphaAPI.data.addPullWatch()`:

```javascript
// extension.js:610
const PULL_SPEC = "[:block/uid :block/string :block/props {:block/children ...}]";

// extension.js:642
const unwatch = window.roamAlphaAPI.data.addPullWatch(PULL_SPEC, query, async function (before, after) {
  // Called whenever block data changes
});
```

When block text changes, it checks if the text starts with a known prefix:

```javascript
// extension.js:184-205
function getBulletTypeByPrefixFromString(blockString) {
  const trimmed = stripLeadingInvisibles(blockString);

  for (const bt of BULLET_TYPES) {
    if (!isBulletTypeEnabled(bt.id)) continue;

    const prefix = getEffectivePrefix(bt);
    const re = bulletSettings.requireSpaceAfterMarker
      ? new RegExp(`^${escapeRegExp(prefix)}${VISIBLE_SPACE_AFTER}`)
      : new RegExp(`^${escapeRegExp(prefix)}`);

    if (re.test(trimmed)) {
      return bt;  // Found a match!
    }
  }
  return null;
}
```

### 2. Saving to Block Props (JavaScript → Roam Database)

Once a bullet type is detected, it's **persisted to the block's props** using `roamAlphaAPI.updateBlock()`:

```javascript
// extension.js:40
const PERSIST_WRITE_KEY = "better-bullets/type";

// extension.js:233-245
function persistType(uid, bulletType) {
  if (!window.roamAlphaAPI || !isValidUid(uid) || !bulletType) return;

  typeCache.set(uid, bulletType.id);
  safeUpdateBlock({
    block: {
      uid,
      props: {
        [PERSIST_WRITE_KEY]: bulletType.id,  // e.g., "arrow", "question", etc.
      },
    },
  });
}
```

**Key insight**: Block props (`:block/props`) is a special attribute in Roam's database that can store arbitrary key-value pairs on any block. The extension stores the bullet type ID (like `"arrow"` or `"question"`) under the key `"better-bullets/type"`.

To read the persisted type back:

```javascript
// extension.js:215-231
function readPersistedType(uid) {
  if (!window.roamAlphaAPI || !isValidUid(uid)) return null;

  try {
    const pulled = window.roamAlphaAPI.pull("[:block/props]", [":block/uid", uid]);
    const props = pulled?.[":block/props"];
    const typeId = getPropValue(props, PERSIST_PROP_TYPE_KEYS);
    return typeof typeId === "string" && typeId ? typeId : null;
  } catch {
    return null;
  }
}
```

### 3. Applying CSS Classes to the DOM (JavaScript → HTML → CSS)

The extension **does NOT rely on CSS reading block props directly**. Instead, it:

1. **Reads** the persisted type from block props (or detects from prefix)
2. **Applies CSS classes and data attributes** to the DOM element
3. **CSS selectors** target these classes to style the bullets

```javascript
// extension.js:404-414
function applyBulletClass(container, typeId) {
  clearBetterBulletClasses(container);

  if (!typeId) {
    container.removeAttribute("data-better-bullet");
    return;
  }

  container.classList.add(`better-bullet-${typeId}`);      // CSS class
  container.setAttribute("data-better-bullet", typeId);   // Data attribute
}
```

This transforms the DOM like this:

```html
<!-- Before -->
<div class="roam-block-container">...</div>

<!-- After (for an arrow bullet) -->
<div class="roam-block-container better-bullet-arrow" data-better-bullet="arrow">...</div>
```

### 4. CSS Uses Classes to Display Icons

The CSS file uses the `::after` pseudo-element to overlay icons on the bullet:

```css
/* extension.css:17-19 - Hide the default bullet dot */
.roam-block-container[class*="better-bullet-"] > .rm-block-main .rm-bullet__inner {
  opacity: 0 !important;
}

/* extension.css:49-63 - Generic styling for the icon container */
.roam-block-container[class*="better-bullet-"] > .rm-block-main .rm-bullet::after {
  position: absolute;
  inset: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: var(--bb-bullet-font-size, 12px) !important;
  /* ... */
}

/* extension.css:65-79 - Specific icons for each bullet type */
.roam-block-container.better-bullet-equal       > .rm-block-main .rm-bullet::after { content: "="; }
.roam-block-container.better-bullet-arrow       > .rm-block-main .rm-bullet::after { content: "→"; }
.roam-block-container.better-bullet-doubleArrow > .rm-block-main .rm-bullet::after { content: "⇒"; }
.roam-block-container.better-bullet-question    > .rm-block-main .rm-bullet::after { content: "?"; }
.roam-block-container.better-bullet-important   > .rm-block-main .rm-bullet::after { content: "!"; }
/* ... etc */
```

## Data Flow Summary

```
User types "-> Hello"
        ↓
JavaScript detects prefix "->" via regex
        ↓
JavaScript calls roamAlphaAPI.updateBlock() to save
{ props: { "better-bullets/type": "arrow" } }
        ↓
JavaScript reads block props and applies
.classList.add("better-bullet-arrow")
        ↓
CSS rule matches .better-bullet-arrow
and displays "→" via ::after pseudo-element
```

## Key Architectural Decisions

### Why Save to Block Props?

1. **Persistence**: Block props survive page refreshes and are synced across devices
2. **Marker Stripping**: If "Strip marker prefix" is enabled, the `-> ` prefix is removed from the text, but the bullet type is preserved in props
3. **No Text Pollution**: The bullet type can be stored without cluttering the block's visible text

### Why Not Use Data Attributes Alone?

Roam's DOM is ephemeral - blocks are constantly re-rendered as you navigate. By storing in block props:
- The type survives DOM changes
- The extension can re-read props and re-apply classes when blocks re-render

### DOM Observation Strategy

The extension uses a `MutationObserver` to watch for new block containers:

```javascript
// extension.js:870-904
function startDomObserver() {
  domObserver = new MutationObserver((muts) => {
    for (const m of muts) {
      if (m.addedNodes?.length) {
        m.addedNodes.forEach((n) => {
          if (n.classList?.contains("roam-block-container")) {
            markContainerDirty(n);  // Queue for processing
          }
          // Also check children...
        });
      }
    }
    if (saw) scheduleDomApplyPass();
  });
}
```

## Bullet Types Available

| ID | Prefix | Icon | Meaning |
|----|--------|------|---------|
| equal | `=` | `=` | Equal / definition |
| arrow | `->` | `→` | Leads to |
| doubleArrow | `=>` | `⇒` | Result |
| question | `?` | `?` | Question |
| important | `!` | `!` | Important / warning |
| plus | `+` | `+` | Idea / addition |
| downRight90 | `v>` | `⤷` | Right-angle arrow |
| contrast | `~` | `≠` | Contrast / however |
| evidence | `^` | `▸` | Evidence / support |
| conclusion | `∴` | `∴` | Conclusion / synthesis |
| hypothesis | `??` | `◊` | Hypothesis / tentative |
| depends | `<-` | `↤` | Depends on / prerequisite |
| decision | `\|` | `⎇` | Decision / choice |
| reference | `@` | `↗` | Reference / related |
| process | `...` | `↻` | Process / ongoing |

## Important: CSS Cannot Read Block Props Directly

**CSS cannot read Roam's database props**. The flow is:

1. JavaScript reads `:block/props` via `roamAlphaAPI.pull()`
2. JavaScript writes CSS classes to the DOM (`element.classList.add()`)
3. CSS selectors match those classes

This is a common pattern in Roam extensions - use JavaScript as the bridge between Roam's database and CSS styling.

## API Methods Used

| Method | Purpose |
|--------|---------|
| `roamAlphaAPI.pull()` | Read block data including props |
| `roamAlphaAPI.updateBlock()` | Write to block props |
| `roamAlphaAPI.data.addPullWatch()` | Watch for changes to blocks |
| `roamAlphaAPI.ui.mainWindow.getOpenPageOrBlockUid()` | Get current page UID |
| `roamAlphaAPI.ui.rightSidebar.getWindows()` | Get sidebar panels |
| `extensionAPI.settings.panel.create()` | Create settings panel |
| `extensionAPI.ui.commandPalette.addCommand()` | Add command palette items |

## Extension Settings

The extension uses Roam's extension settings API:

```javascript
// extension.js:1156-1254
function buildSettingsConfig(extensionAPI) {
  const settings = [];

  settings.push({
    id: "bb-require-space",
    name: "Require a space after marker",
    description: "...",
    action: {
      type: "switch",
      value: bulletSettings.requireSpaceAfterMarker,
      onChange: (e) => { /* ... */ },
    },
  });
  // ... more settings
}
```

## Conclusion

Better Bullets demonstrates a clean pattern for Roam extensions:

1. **Watch** for changes with `addPullWatch()`
2. **Persist** state to `:block/props` for durability
3. **Apply** CSS classes via JavaScript when blocks render
4. **Style** using CSS selectors that target those classes

This separation of concerns (data in props, presentation via CSS classes) makes the extension robust against Roam's dynamic DOM updates.
