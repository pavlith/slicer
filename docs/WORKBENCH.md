# Workbench UI contract

## Regions

Stable region identifiers are header, left, primary, secondary, editor, panel, footer. They are layout roles, not domain-specific component names.

    +--------------------------- header --------------------------+
    | left | primary |              editor          | secondary |
    |      |         |        tabs / editor groups  |           |
    |      |         +-------------- panel ---------+           |
    +--------------------------- footer --------------------------+

| Region | Role | Behavior |
| --- | --- | --- |
| header | Window/menu actions, global search/command entry | Compact, host owns native window controls where required |
| left | Activity Bar, navigation entries contributed by owners | Icon rail; selection controls primary views; keyboard accessible |
| primary | Main navigation/views | Hide, collapse, resize; no hard-coded Mail |
| secondary | Context, properties, assistant views | Hide, collapse, resize; optional by default |
| editor | Main tab bar and content, editor groups | Always available; empty state; independent group focus |
| panel | Bottom tabbed output/diagnostics/tasks | Hide, collapse, resize; no terminal emulator in MVP |
| footer | Status bar, notification entry, status contributions | Left/right aligned, compact |

Use PaneForge for primary/secondary/panel. Editor area must not disappear if every extension is disabled. Persist visibility and dimensions with safe minimum/maximum bounds, restore invalid state to defaults. Header/footer may offer visibility settings later; MVP keeps essential window controls accessible. Do not force all zones to contain something. Left rail active state uses a neutral accent or indicator, not large colored icon backgrounds.

## UI standards

Use Svelte 5 runes for new reactive state where appropriate, TypeScript strictness, current shadcn-svelte primitives and Tailwind semantic colors. Neutral Zinc, light/dark/system via Mode Watcher. Prefer Lucide icons, accessible names, keyboard focus indicators, compact spacing and restrained transitions. No IBM Carbon dependency or theme. Avoid duplicating components or creating a second design system. Use Formsnap for forms when useful, Runed for reusable reactive helpers, Virtua for measured long-list needs, and the existing svelte-dnd-action where appropriate. Do not add libraries without a real consumer.

## Tab and editor model

Proposed contract (illustrative TypeScript, refine at Stage 03):

    interface EditorTab {
      id: string;          // unique instance
      ownerId: string;     // module/extension
      editorType: string;  // registered contribution ID
      resource?: string;   // opaque stable owner-scoped reference
      title: string;
      groupId: string;
      pinned: boolean;
      preview: boolean;
      dirty: boolean;
    }

    interface EditorGroup {
      id: string;
      tabIds: string[];
      activeTabId: string | null;
    }

Registry resolves editor types to renderers; no switch statement on extension identities in the shell. Core knows opaque resource references, not mail IDs. Stable resource identity plus type and group support open-or-focus deduplication; an explicit new instance may be requested. Dirty state is supplied by the owner. Never close dirty content silently, including close-all, disable/uninstall or shutdown. Pinned tabs are protected from bulk close but can be explicitly closed.

MVP tab operations: open/focus, close, close others/right/all, pin/unpin, reorder, preview replacement, promote preview on double click or edit, next/previous, reopen closed where resolvable. One preview per group, never replace a pinned or dirty tab. On close select a predictable adjacent tab. Handle unavailable owner/renderer with a safe placeholder and close/retry actions. Preserve scroll/form state explicitly where needed; mount active view lazily. Do not persist rendered HTML or sensitive resource data.

Start with one group; add a bounded horizontal split with per-group focus and move tab across groups at Stage 03. Arbitrary recursive docking is later. Serialize layout and restorable tabs by ID/resource/version; skip missing providers or show a placeholder rather than crashing. Restore only after registry is ready. Persist non-secret metadata and validate all restored references.

Keyboard defaults: Ctrl+Tab/Shift+Ctrl+Tab next/previous; Ctrl+W closes focused tab (not window); Ctrl+Shift+T reopens; Ctrl+Shift+P opens command palette; Ctrl+P opens quick search. Input/editor shortcuts take precedence where appropriate. Accessible drag-and-drop alternatives and context menus are required. Do not globally suppress browser/native context menus or override text editing shortcuts without a concrete need.

## Contributions

The shell accepts owner-scoped registrations for activity entries, views, editor types, header actions, panel views, footer status items and command/search registrations. Stable namespaced IDs, order, region, title and icon are metadata. Every registration returns a disposer, rejects duplicates and is removed when the owner stops. Use one shared registry contract for modules and extensions; trusted module components can lazy-import local Svelte files, extension UI goes through the isolated rendering mechanism described in [EXTENSIONS.md](EXTENSIONS.md).

No direct import from extensions into App.svelte or layout components. An unloaded renderer shows a loading/error state. Empty regions collapse gracefully. A test contribution should prove the shell can accept unknown types without editing the shell.
