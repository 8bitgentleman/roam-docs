# Roam Research Developer Documentation

A collection of documentation for building extensions, components, and integrations with Roam Research.

## LLM Navigation Guide

This README helps you find the right documentation quickly without reading every file. Documents are organized by use case.

---

## Quick Reference: Which Doc Do I Need?

| If you need to...                                      | Read this document                          |
|-------------------------------------------------------|---------------------------------------------|
| Query or modify blocks/pages in the browser           | `Docs - Roam Alpha API.md`                  |
| Build a backend integration or server-side app        | `Docs - Roam Backend API (Beta).md`         |
| Quickly append/capture data to Roam                   | `Docs - Roam Append API.md`                 |
| Create inline custom components (`{{roam/render}}`)   | `Docs - roam-render.md`                     |
| Write datalog queries                                 | `Docs - Examples of RoamAlphaAPI datalog queries.md` |
| Look up block/page attribute names for queries        | `Roam Research Block Datalog Props.md`      |
| Submit an extension to Roam Depot                     | `Docs - Roam Depot-Extensions.md`           |
| Check what libraries are bundled in Roam              | `Docs - Roam Available Libraries.md`        |

---

## Documentation by Category

### 1. Client-Side APIs (Browser)

Use these when building extensions that run in the Roam browser interface.

#### `Docs - Roam Alpha API.md` (~1,900 lines)
**The main reference for browser-based development.** Covers the `roamAlphaAPI` object including:
- Reading data (`q`, `pull`, `data.pull`, `data.fast`)
- Writing data (`createPage`, `createBlock`, `updateBlock`, `moveBlock`, `deleteBlock`)
- UI manipulation (sidebar, main window, block focus)
- Parameter schemas and location objects

#### `Docs - Roam Depot-Extension API.md` (~30 lines)
Minimal reference for extension-specific APIs:
- Settings management (`extensionAPI.settings`)
- Panel creation
- Command palette integration

#### `Docs - Roam Available Libraries.md` (~60 lines)
Lists all bundled dependencies available globally:
- React, ReactDOM, Blueprint.js, date-fns, ChronoNode, idb-keyval, etc.
- Includes version numbers and global variable names

---

### 2. Server-Side APIs (Backend)

Use these for server-to-server integrations or when you need authenticated API access outside the browser.

#### `Docs - Roam Backend API (Beta).md` (~1,000 lines)
**Complete backend API reference.** Covers:
- Authentication with `roam-graph-token`
- Query endpoints and write operations
- Sharding mechanism
- SDK availability (TypeScript, Clojure, Python, Java)
- Full request/response schemas

#### `Docs - Roam Append API.md` (~420 lines)
**Simplified API for data capture.** Use when you only need to append data:
- Append-only tokens (more secure for automations)
- Auto-creates pages if they don't exist
- `nest-under` functionality for organizing captures
- Exactly-once write guarantees

---

### 3. Custom Components (roam/render)

Use these when building inline interactive components that render inside Roam blocks.

#### `Docs - roam-render.md` (~380 lines)
**Practical getting-started guide.** Covers:
- Creating render blocks in ClojureScript, JavaScript, or JSX
- Referencing components with `{{roam/render: ((block-ref))}}`
- Hello World examples
- Argument parsing from block content

#### `Article - a_closer_look_at_roam render.md` (~420 lines)
**Deep-dive educational article.** Read this to understand:
- The underlying technologies (React, Reagent, Hiccup, Atoms, Datomic)
- Why roam/render works the way it does
- Key concepts for beginners coming from other frameworks

#### `Docs - Roam Clojurescript API.md` (~700 lines)
**ClojureScript-specific API reference.** Covers:
- Available Clojure core namespaces
- `roam.datascript` and `roam.datascript.reactive` namespaces
- Reactive queries that auto-update components
- Differences from JavaScript API

---

### 4. Datalog Queries

Use these when you need to query Roam's data using Datalog.

#### `Roam Research Block Datalog Props.md` (~170 lines)
**Property reference table.** Lists all queryable attributes:
- `:block/string`, `:block/uid`, `:block/children`, `:block/refs`
- `:node/title`, `:create/time`, `:edit/time`
- Essential lookup for constructing queries

#### `Docs - Examples of RoamAlphaAPI datalog queries.md` (~270 lines)
**JavaScript code examples.** Includes:
- Finding pages with specific tags
- Regex matching in queries
- Filtering and sorting results
- Copy-paste ready code snippets

#### `Docs - Examples of 'q' query blocks.md` (~250 lines)
**Native Roam `:q` block examples.** Covers:
- Query syntax within Roam itself (not via API)
- Counting pages, TODOs
- Output formats (single value vs. table)
- Advanced query features

#### `Docs - datalog-block-query.md` (~150 lines)
**Advanced datalog features.** Covers:
- Rules (reusable logical abstractions)
- Using `{{queries}}` as input
- Block references as queries

#### `Docs - available datascript built ins.md` (~40 lines)
**Quick lookup** for available aggregate and query functions in datascript.

---

### 5. Extension Development

Use these when building and distributing Roam extensions.

#### `Docs - Roam Depot-Extensions.md` (~110 lines)
**Official guide for Roam Depot submissions.** Covers:
- Code structure (`onload`/`onunload` functions)
- UI guidelines
- CSS styling practices
- Security review process
- Revenue sharing information

#### `roamjs-compoents_overview.md` (~190 lines)
**RoamJS component library overview.** Documents:
- Pre-built components (AutocompleteInput, BlockInput, etc.)
- DOM utilities
- Database and event utilities
- Useful if building on RoamJS infrastructure

---

## Document Size Reference

For context-window planning:

| Size       | Documents                                                                                          |
|------------|----------------------------------------------------------------------------------------------------|
| Large      | `Docs - Roam Alpha API.md` (1,885 lines), `Docs - Roam Backend API (Beta).md` (1,012 lines)       |
| Medium     | `Docs - Roam Clojurescript API.md` (692), `Docs - Roam Append API.md` (418), `roam-render.md` (381), `Article - a_closer_look_at_roam render.md` (416) |
| Small      | Everything else (27-273 lines)                                                                     |

---

## Common Tasks

### "I want to build a Roam extension"
1. Start with `Docs - Roam Depot-Extensions.md` for structure
2. Reference `Docs - Roam Alpha API.md` for available methods
3. Check `Docs - Roam Available Libraries.md` for bundled dependencies

### "I want to query Roam's database"
1. Look up attributes in `Roam Research Block Datalog Props.md`
2. Find example queries in `Docs - Examples of RoamAlphaAPI datalog queries.md`
3. Reference `Docs - Roam Alpha API.md` for the `q` and `pull` methods

### "I want to build a custom component"
1. Start with `Docs - roam-render.md` for the basics
2. Read `Article - a_closer_look_at_roam render.md` for deeper understanding
3. Reference `Docs - Roam Clojurescript API.md` for ClojureScript-specific APIs

### "I want to integrate Roam with an external service"
1. For append-only operations: `Docs - Roam Append API.md`
2. For full read/write: `Docs - Roam Backend API (Beta).md`

---

## File Naming Convention

- `Docs - *.md` — Official or reference documentation
- `Article - *.md` — Educational deep-dive content
- Other names — Supplementary reference material
