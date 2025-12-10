# RoamJS Components Library Overview

This document provides a comprehensive overview of the modules and components available in the RoamJS Components library, a collection of utilities and React components for developing extensions for Roam Research.

**Version:** 0.85.7
**License:** MIT
**Repository:** https://github.com/RoamJS/roamjs-components

## Overview

The RoamJS Components library is an expansive toolset containing:
- **20+ React Components** for UI development
- **15+ Configuration Panel Components** for settings pages
- **50+ Database Query Functions** for data retrieval
- **35+ DOM Manipulation Utilities** for UI interaction
- **45+ Utility Functions** for common tasks
- **10+ Write Operations** for data modification
- **Custom Hooks** for React development
- **Testing Utilities** for extension testing
- **Markdown Parser** with Roam-specific syntax support

## Import Pattern

The library uses **direct file imports** to prevent import bloat. Always import from specific module files:

```typescript
// ✓ Correct - individual file imports
import AutocompleteInput from "roamjs-components/components/AutocompleteInput"
import getTextByBlockUid from "roamjs-components/queries/getTextByBlockUid"
import createBlock from "roamjs-components/writes/createBlock"
import runExtension from "roamjs-components/util/runExtension"

// ✗ Incorrect - barrel imports will throw errors
import { AutocompleteInput } from "roamjs-components/components"
import { getTextByBlockUid } from "roamjs-components/queries"
```

## Components (`src/components`)

This directory contains reusable React components designed to integrate seamlessly with the Roam Research UI.

**Important:** Components must be imported individually from their specific files, not from the components directory. The library uses direct file imports to prevent import bloat:

```typescript
// ✓ Correct
import AutocompleteInput from "roamjs-components/components/AutocompleteInput"
import FormDialog from "roamjs-components/components/FormDialog"

// ✗ Incorrect - will throw an error
import { AutocompleteInput, FormDialog } from "roamjs-components/components"
```

### AutocompleteInput.tsx
Implements a generic autocomplete input field. It is highly customizable, supporting various options, multiline input, and fuzzy search for filtering. The component manages its own state, handles keyboard navigation, and displays a popover menu with selectable options, including support for creating new items.

### BlockErrorBoundary.tsx
A React error boundary component designed for wrapping custom components rendered in Roam blocks. If an error occurs within its children, it catches the error, logs it by creating a new block in Roam with an error message, and then displays a fallback UI.

### BlockInput.tsx
Provides a specialized search input for finding Roam Research blocks. As a user types, it uses regex to search all blocks and displays a menu of matching results. It supports selection via keyboard or mouse and is ideal for quick block referencing in forms or dialogs.

### ComponentContainer.tsx
A wrapper component that adds editing functionality to its children when hovered. It can focus and select the underlying Roam block when an edit icon is clicked. This is often used to embed custom components into Roam blocks while providing additional UI controls.

### ConfigPage.tsx
The main configuration page component for RoamJS extensions. It supports both legacy and new configuration structures, renders various field types and tabs, and manages the enabling and disabling of features. It also includes utilities for rendering and observing configuration pages, allowing for dynamic updates.

### CursorMenu.tsx
Implements a popover menu that appears at the cursor's position within a textarea. It allows users to select from a list of filtered options, using fuzzy search and keyboard navigation. The component integrates with Roam block updates and is useful for features like autocomplete or user mentions.

### Description.tsx
Renders a small info icon that displays a descriptive tooltip on hover. This component is used to annotate UI elements with additional context or help text in a styled tooltip.

### ExtensionApiContext.tsx
Provides a React context for accessing the RoamJS extension API and its version. It includes hooks that allow any component in the tree to consume the API and version information. **Note:** This component is marked as deprecated in favor of the `extensionApiContext` utility in `src/util/extensionApiContext.ts`.

### ExternalLogin.tsx
Handles OAuth and other external authentication workflows. It manages pop-out login windows, state, and authentication data, with support for both local and remote account storage. The component displays the login UI, handles success and error states, and integrates with Roam blocks for storing credentials.

### Filter.tsx
Implements a user interface for building complex filters with "includes" and "excludes" logic across multiple categories. It supports fuzzy search, toggling filters, and displays the active filter state with color cues. It is used to filter data sets in UI views, such as tables or search results.

### FormDialog.tsx
A generic dialog component for creating forms. It supports a wide variety of field types, including text, number, select, page, block, autocomplete, checkbox (flag), and embeddable content. The dialog handles field dependencies, conditional rendering, asynchronous submission, and is deeply integrated with Roam for fetching block and page data. It also includes utility methods for rendering overlays and prompting for user input.

### Loading.tsx
A simple loading spinner component with a `renderLoading()` function. Can render a spinner UI and supports targeting specific UIDs for block-level loading indicators.

### MenuItemSelect.tsx
A generic menu select component built on Blueprint's Select. Supports filtering, custom item transformation, and custom children rendering. Highly configurable with custom button props and item predicate filtering.

### OauthPanel.tsx
Configuration component for OAuth account management. Displays a list of connected accounts with logout functionality. Uses local storage for account persistence and integrates with the OAuth configuration system.

### OauthSelect.tsx
Provides the `useOauthAccounts` hook for managing OAuth account dropdowns and selection. Handles account retrieval and selection state management.

### PageInput.tsx
A specialized wrapper around AutocompleteInput specifically for page selection. Auto-populates options from all page names in the graph, providing an easy way to select existing pages in forms and dialogs.

### PageLink.tsx
React component for rendering clickable page reference links. Supports shift+click to open in sidebar and ctrl+click for custom handlers. Handles page title resolution and URL generation automatically.

### ProgressDialog.tsx
Displays progress during batch write operations. Tracks remaining actions and displays expected completion time. Exported via `render()` function and includes an `ID` constant for targeting the dialog.

### SimpleAlert.tsx
A customizable alert component with markdown rendering support via `render()` function. Supports "Don't show again" checkbox functionality and external link handling. Useful for displaying notifications and confirmations.

### Toast.tsx
Toast notification component exported via `render()` function. Supports markdown rendering with custom styling, positioning options (top/bottom), intent levels (primary, success, warning, danger), timeouts, and custom action buttons. Uses an event-based system for displaying multiple toasts.

## Configuration Panels (`src/components/ConfigPanels`)

This subdirectory contains specialized panel components for building configuration pages. Each panel corresponds to a specific field type and handles rendering, validation, and data management.

### Panel Components

#### BlockPanel.tsx
Panel for single block input in configuration. Allows users to select or reference a specific Roam block.

#### BlocksPanel.tsx
Panel for multiple block inputs. Supports adding, removing, and managing an array of block references.

#### CustomPanel.tsx
Generic panel wrapper for custom field components. Allows extensions to inject their own custom configuration UI elements.

#### FlagPanel.tsx
Checkbox/toggle panel for boolean flags. Renders a switch or checkbox for true/false configuration values.

#### MultiChildPanel.tsx
Panel for managing multiple child configuration nodes. Handles hierarchical configuration structures.

#### MultiTextPanel.tsx
Panel for multiple text input fields. Supports adding, removing, and reordering multiple text values.

#### NumberPanel.tsx
Panel for numeric input with validation. Supports min/max constraints and step values.

#### PagesPanel.tsx
Panel for selecting multiple pages from the graph. Provides autocomplete and validation for page references.

#### SelectPanel.tsx
Dropdown selection panel with predefined options. Supports single-select from a list of valid values.

#### TextPanel.tsx
Simple text input panel for string configuration values. The most common panel type.

#### TimePanel.tsx
Specialized time picker panel for time-based configuration values.

### Panel Utilities

#### getBrandColors.tsx
Utility function for retrieving Roam's brand colors. Used to style panels consistently with Roam's UI.

#### useSingleChildValue.tsx
Custom React hook for managing single child value extraction from configuration trees. Simplifies working with tree-based configuration data.

#### types.ts
Comprehensive type definitions for all field panel types:
- `TextField`, `TimeField`, `NumberField`, `FlagField` - Basic field types
- `MultiTextField`, `PagesField`, `SelectField` - Multi-value field types
- `BlockField`, `BlocksField`, `CustomField`, `OauthField` - Advanced field types
- `FieldPanel` - Base panel interface
- `UnionField` - Union type of all field types

## DOM Utilities (`src/dom`)

This directory provides a collection of utility functions and constants for interacting with the Document Object Model (DOM) within Roam Research. These helpers enable developers to manipulate the UI, observe changes, and work with Roam-specific data attributes.

### Key Functionalities

*   **Adding Block Commands & Script Dependencies**:
    *   `addBlockCommand`: Adds a new command to the command palette for a specific block.
    *   `addKeyboardTriggers`: Sets up keyboard trigger callbacks for block input. Supports RegExp, function, or tree-based triggers that fire on textarea input matching specific patterns.
    *   `addRoamJSDependency`, `addOldRoamJSDependency`, `addScriptAsDependency`: Manage and ensure required scripts are loaded for an extension to function correctly.

*   **DOM Observation & Manipulation**:
    *   `createBlockObserver`, `createHTMLObserver`, `createPageTitleObserver`, `createButtonObserver`, `createDivObserver`, `createOverlayObserver`, `createHashtagObserver`, `createPageObserver`: A suite of functions that use `MutationObserver` to monitor changes to specific parts of the Roam DOM and trigger callback functions.
    *   `createHashtagObserver`: Observes hashtag span elements and adds attributes to prevent duplicate processing.
    *   `createPageObserver`: Monitors block additions and removals on a specific page, tracking which blocks belong to that page.

*   **Retrieving & Manipulating Roam-Specific Data**:
    *   `getCurrentPageUid`, `getBlockUidFromTarget`, `getUids`, `getUidsFromId`, `getUidsFromButton`, `getActiveUids`: Functions to retrieve unique identifiers (UIDs) for pages and blocks from various DOM elements and contexts.
    *   `getUidsFromButton`: Specifically extracts UIDs from button DOM elements.
    *   `getDomRefs`: Extracts block references from the currently editing block, returning an array of referenced block UIDs.

*   **URL Generation & Reference Resolution**:
    *   `getRoamUrl`, `getRoamUrlByPage`: Generate sharable URLs for specific Roam pages or blocks.
    *   `resolveRefs`: Resolves Roam-style block references or aliases to their corresponding URLs or content.

*   **Parsing Roam Block Content**:
    *   `parseRoamBlocksToHtml`: Converts the content of Roam blocks from Roam's format into standard HTML, which is useful for exporting or custom rendering.

*   **Utility Functions**:
    *   `getReferenceBlockUid`: Finds the UID of a referenced block from a given DOM element.
    *   `addStyle`: Injects a string of CSS styles into the document's head.
    *   `createIconButton`: A helper to generate icon buttons that match Roam's UI style.
    *   `elToTitle`, `getPageTitleValueByHtmlElement`: Extract the title or text content from DOM nodes.
    *   `getDropUidOffset`: Calculates the parent UID and drop offset for elements that are dragged and dropped in the UI.
    *   `getMutatedNodes`: Works with and identifies nodes that have been changed in the DOM.

*   **Constants**:
    *   `BLOCK_REF_REGEX`: A regular expression for identifying block references in Roam.
    *   Other constants are provided for various DOM operations.

## Event Handling (`src/events`)

This directory centralizes utilities for setting up and managing event listeners in Roam Research. It allows developers to respond to changes within the application's data model.

### Key Functionalities

*   **`watchOnce`**:
    *   This function sets up a one-time watcher on a specific Roam Research block.
    *   It listens for changes that match a given `pullPattern` and a specific block ID (`entityId`).
    *   When a matching change is detected, it executes a callback function with the state of the data before and after the change.
    *   If the callback function returns `true`, the watcher automatically removes itself.
    *   This is particularly useful for cases where an action should only be triggered once, such as updating a visualization or sending a notification after a specific block is updated.

## Date Utilities (`src/date`)

This directory provides helper functions for parsing and manipulating dates and times, which is essential for any feature that interacts with Roam's daily notes or date-based data.

### Key Functionalities

*   **Natural Language Date Parsing**:
    *   `parseNlpDate`: Uses the `chrono-node` library to interpret natural language date strings (e.g., "yesterday," "next Friday"). It also includes custom refinements for phrases like "start of week" or ordinal weekdays in a month (e.g., "first Monday of June").

*   **Roam Date UID Parsing**:
    *   `parseRoamDateUid`: Converts a Roam Research date UID (which follows a "month-date-year" format) into a standard JavaScript `Date` object.

*   **Constants**:
    *   `MONTHS`: An array containing the names of the months.
    *   `DAILY_NOTE_PAGE_REGEX` and `DAILY_NOTE_PAGE_TITLE_REGEX`: Regular expressions used for matching and validating the titles of daily note pages.

## React Hooks (`src/hooks`)

This directory contains custom React hooks for managing user interactions and complex data structures within RoamJS extensions.

### Key Functionalities

*   **`useArrowKeyDown`**: Handles keyboard navigation for lists (like autocomplete menus). It tracks the active item index and provides logic for `ArrowDown`, `ArrowUp`, and `Enter` key presses to cycle through and select items.
*   **`useSubTree`**: Manages sub-tree data structures from Roam blocks. It uses React's `useMemo` hook to efficiently compute and update the sub-tree data based on dependencies like a parent UID, returning the current sub-tree and a function to update it.

## Markdown Parsing (`src/marked`)

This directory provides customized Markdown parsing and rendering for Roam Research, building upon the `marked` library to handle Roam-specific syntax and elements.

### Key Functionalities

*   **Language Syntax Highlighting**: Uses the `refractor` library to provide syntax highlighting for various languages in code blocks.
*   **Custom Regular Expressions**: Defines regex patterns for Roam-specific elements like `[[TODO]]`/`[[DONE]]`, block references, tags, aliases, and attributes.
*   **Custom Renderer**: Extends the `marked` renderer to correctly handle Roam-specific links, aliases, tags, and other custom elements.
*   **Contextual Parsing**: Allows Markdown parsing to adapt to Roam-specific page and block references, providing context-aware parsing functions.

## Database Queries (`src/queries`)

This directory provides utility functions for querying and interacting with the Roam Research database. These functions enable data retrieval, manipulation, and normalization. With over 50 specialized query functions, this is the most comprehensive module in the library.

### Block Retrieval Functions

*   `getAllBlockUids`: Retrieves all block UIDs in the graph
*   `getAllBlockUidsAndTexts`: Gets all blocks with their text content
*   `getBlockUidAndTextIncludingText`: Searches blocks by text content, returns matching blocks
*   `getBlockUidByTextOnPage`: Finds a specific block by text on a given page
*   `getBlockUidsWithParentUid`: Gets all blocks with a specific parent UID
*   `getBlockUidsByPageTitle`: Gets all blocks on a specific page
*   `getFirstChildUidByBlockUid`: Returns the UID of the first child block
*   `getFirstChildTextByBlockUid`: Returns the text of the first child block
*   `getNthChildUidByBlockUid`: Gets the nth child block by index
*   `getTextByBlockUid`: Retrieves the text content of a block
*   `getOrderByBlockUid`: Gets the order/position value of a block
*   `getCreateTimeByBlockUid`: Returns the creation timestamp of a block
*   `getEditTimeByBlockUid`: Returns the last edit timestamp of a block

### Page Retrieval Functions

*   `getAllPageNames`: Gets all page titles in the graph
*   `getPageTitleByBlockUid`: Gets the parent page title for a block
*   `getPageTitleByPageUid`: Direct page title lookup by page UID
*   `getPageUidByBlockUid`: Gets the parent page UID from a block
*   `getPageUidByPageTitle`: Looks up page UID by page title
*   `getPageTitlesStartingWithPrefix`: Searches pages by title prefix
*   `getPageViewType`: Gets the view mode for a page (document, bullet, etc.)

### Reference and Link Functions

*   `getBlockUidsReferencingBlock`: Gets all blocks that reference a specific block
*   `getBlockUidsReferencingPage`: Gets all blocks that reference a specific page
*   `getBlockUidsAndTextsReferencingPage`: Gets blocks referencing a page with their text content
*   `getPageTitlesReferencingBlockUid`: Gets page titles that reference a specific block
*   `getPageTitlesAndBlockUidsReferencingPage`: Gets comprehensive reference info for a page
*   `getPageTitlesAndUidsDirectlyReferencingPage`: Gets only direct page references (no nested)
*   `getPageTitleReferencesByPageTitle`: Gets all references mentioned on a page
*   `getLinkedPageTitlesUnderUid`: Gets page references in a block hierarchy

### Hierarchy and Tree Functions

*   `getParentUidByBlockUid`: Gets the direct parent UID of a block
*   `getParentUidsOfBlockUid`: Gets all ancestor UIDs (full path to root)
*   `getParentTextByBlockUid`: Gets the parent block's text content
*   `getParentTextByBlockUidAndTag`: Gets parent text with tag filtering
*   `getBasicTreeByParentUid`: Gets basic tree structure (shallow)
*   `getShallowTreeByParentUid`: Gets shallow tree (one level deep)
*   `getFullTreeByParentUid`: Gets complete tree structure with all descendants
*   `getChildrenLengthByParentUid`: Counts direct children of a block
*   `getChildrenLengthByPageUid`: Counts direct children of a page

### User Information Functions

*   `getCurrentUser`: Gets the full current user object
*   `getCurrentUserUid`: Gets the current user's UID
*   `getCurrentUserEmail`: Gets the current user's email address
*   `getCurrentUserDisplayName`: Gets the current user's display name
*   `getDisplayNameByUid`: Looks up display name by user UID
*   `getDisplayNameByEmail`: Looks up display name by email address
*   `getSettingsByEmail`: Gets user settings by email
*   `getEditedUserEmailByBlockUid`: Gets the email of the last user to edit a block

### Attribute and Metadata Functions

*   `getAttributeValueByBlockAndName`: Retrieves custom attribute values from blocks using Datalog
*   `isTagOnPage`: Checks if a specific tag exists on a page
*   `isLiveBlock`: Checks if a block is currently live/active

### Query Compilation and Utilities

*   `compileDatalog`: Converts Datalog query objects into string format for querying
*   `normalizePageTitle`: Escapes and standardizes page titles for safe use in queries

## Scripts (`src/scripts`)

This directory contains scripts for managing RoamJS extension publishing and development workflows, including command-line utilities.

### Key Functionalities

*   **CLI Entry Point**: The `index.ts` file serves as the command-line interface entry point for building, testing, and deploying RoamJS extensions.
*   **Extension Publishing Automation**: `publishToRoamDepot.ts` automates publishing an extension to the Roam Depot. It handles checking for existing pull requests, cloning the depot repository, updating the extension manifest, and creating or updating pull requests on GitHub.

## Testing (`src/testing`)

This directory provides utilities for testing RoamJS extensions in a mock environment.

### Key Functionalities

*   **`mockRoamEnvironment.ts`**: A comprehensive 58KB testing utility that provides:
    *   **Mock Roam Database Environment**: Simulates Roam's database and API for unit testing
    *   **EDN Parsing and Datalog Query Simulation**: Parses and executes Datalog queries against mock data
    *   **Full Graph State Management**: Maintains mock graph state including blocks, pages, and relationships
    *   **Block and Page Creation**: Supports creating test blocks and pages with proper UID generation
    *   **Query Testing**: Allows testing of query functions without requiring a live Roam instance

    This is essential for writing comprehensive unit tests for extensions without needing to run against a real Roam Research database.

## Type Definitions (`src/types`)

This directory defines the core TypeScript types and interfaces for the RoamJS extension ecosystem, ensuring type safety across the library.

### Key Functionalities

*   **Centralized Type Definitions**: A main index file exports types from various submodules. A central `Registry` type manages state for listeners, commands, and other extension elements.
*   **Roam Research Data Structures**: Models for `RoamBasicBlock`, `RoamBasicPage`, and other core Roam data structures.
*   **Datalog and Query Builder Types**: Types for modeling Datalog queries and their results.
*   **SmartBlocks & Client-Side Actions**: Definitions for SmartBlocks commands and parameters for client-side actions like creating or updating blocks.
*   **User Settings and Sidebar**: Types for modeling user settings and sidebar UI components.
*   **Global Window Extensions**: Extends the global `Window` object to include the `roamAlphaAPI` and the `roamjs` namespace for cross-extension communication.

## Utility Functions (`src/util`)

This directory provides a comprehensive set of utility functions that support almost all aspects of RoamJS extension development.

### Key Functionalities

*   **API Interaction**:
    *   `apiGet`: HTTP GET request wrapper with authorization support
    *   `apiPost`: HTTP POST request wrapper with authorization support
    *   `apiPut`: HTTP PUT request wrapper with authorization support
    *   `apiDelete`: HTTP DELETE request wrapper with authorization support
    *   `handleFetch`: Generic fetch handler with authorization, buffer support, error handling, and support for JSON, text, and ArrayBuffer responses
    *   `handleBodyFetch`: Specialized fetch for request bodies
    *   `handleUrlFetch`: Specialized fetch for URL-based requests

*   **DOM and Rendering**:
    *   `createOverlayRender`: Creates a render function for overlay components
    *   `renderOverlay`: Renders overlay components
    *   `renderWithUnmount`: Renders React component with automatic unmounting support
    *   `getRenderRoot`: Gets the React render root for a container
    *   `focusMainWindowBlock`: Sets focus and selection on a specific block in the main window

*   **Data Extraction and Transformation**:
    *   `extractRef`: Extracts block references from text
    *   `extractTag`: Extracts hashtags from text
    *   `createTagRegex`: Creates regex pattern for tag matching
    *   `toFlexRegex`: Creates flexible regex for fuzzy matching
    *   `idToTitle`: Converts ID format to page title format
    *   `stripUid`: Removes UID from block reference format
    *   `toConfigPageName`: Converts extension name to config page name format

*   **Settings and Configuration**:
    *   `addInputSetting`: Adds new settings to configuration
    *   `setInputSetting`: Sets a single setting value
    *   `setInputSettings`: Sets multiple settings at once
    *   `getSettingValueFromTree`: Retrieves setting values from config tree
    *   `getSettingValuesFromTree`: Gets multiple setting values
    *   `getSettingIntFromTree`: Gets integer setting values
    *   `getSubTree`: Extracts configuration subtree

*   **Local Storage Management**:
    *   `localStorageGet`: Wrapper for localStorage.getItem with graph-aware key handling
    *   `localStorageSet`: Wrapper for localStorage.setItem with graph-aware key handling
    *   `localStorageRemove`: Wrapper for localStorage.removeItem with graph-aware key handling
    *   `getLocalStorageKey`: Generates localStorage keys with optional graph prefix

*   **OAuth and Authentication**:
    *   `getOauth`: Gets OAuth configuration for a service
    *   `getOauthAccounts`: Retrieves stored OAuth accounts from configuration
    *   `getToken`: Retrieves authentication token
    *   `getTokenFromTree`: Extracts token from configuration tree
    *   `getAuthorizationHeader`: Generates Authorization header for API requests

*   **Extension Management**:
    *   `runExtension`: Main extension lifecycle manager that handles setup, initialization, and cleanup
    *   `extensionApiContext`: Extension API context provider utility
    *   `extensionDeprecatedWarning`: Displays deprecation warnings for extensions
    *   `dispatchToRegistry`: Dispatches lifecycle events to extension registry
    *   `removeFromRegistry`: Removes listeners and handlers from extension registry
    *   `registerSmartBlocksCommand`: Registers commands with SmartBlocks extension, returns unregister function
    *   `registerExperimentalMode`: Manages experimental feature flags with warnings, creates enable/disable commands in command palette

*   **Environment and Configuration**:
    *   `getNodeEnv()`: Returns NODE_ENV value
    *   `getRoamJSVersionEnv()`: Returns ROAMJS_VERSION
    *   `getApiUrlEnv()`: Returns API URL (production vs development)
    *   `getRoamJSExtensionIdEnv()`: Returns extension ID

*   **Miscellaneous Utilities**:
    *   `isControl`: Checks if a key press is a control key (Ctrl/Cmd)
    *   `getWorkerClient`: Gets Web Worker client interface for background processing

## Database Writes (`src/writes`)

This directory contains functions for performing write operations on the Roam Research database, such as creating, updating, and deleting content.

### Key Functionalities

*   **Block Operations**:
    *   `createBlock`, `updateBlock`, `deleteBlock`: Create, modify, or remove a block by its UID.
    *   `clearBlockById`/`clearBlockByUid`: Clear the content of a block.
    *   `updateActiveBlock`: Update the content of the currently focused block.
*   **Page Operations**:
    *   `createPage`: Creates a new page, with support for daily note UID generation.
*   **Sidebar Operations**:
    *   `openBlockInSidebar`: Opens a specific block in the right sidebar.
*   **Action Submission**: All write operations use a central `submitActions` function that queues actions and includes built-in retry logic to ensure reliable API interactions.