# Target architecture

Read [VISION.md](VISION.md) first. This is a target, not an inventory of current files.

## Boundaries

    Workbench (Svelte)
        | typed Wails host API / events
    Core + trusted Modules (Go / Svelte)
        | versioned SDK protocol, capability mediation
    Extension Host
        | supervised IPC + isolated UI channel
    Independently installed Extensions

Core owns startup/shutdown, registry and platform facilities. Modules supply reusable services, and may register contributions. Workbench owns presentation and user layout state. Extensions own domain logic, domain storage and domain UI. The composition root wires concrete implementations; modules depend on small interfaces rather than importing each other cyclically. The public SDK must not import internal host implementation packages or Wails-generated app bindings.

## Incremental target repository layout

    slicer/
      main.go
      go.mod
      app/                    # composition root and Wails bindings
      core/                   # lifecycle, registry, platform, IPC
      modules/
        settings/
        notify/
        logger/
        search/
        action/
        extensions/           # management/host facade
      sdk/
        go/
        typescript/
        schemas/
      frontend/
        package.json
        src/
          App.svelte
          lib/
            components/ui/    # standard shadcn-svelte primitives
            workbench/
              header/
              left/
              primary/
              secondary/
              editor/
              panel/
              footer/
              layout/
              tabs/
              registry/
            modules/          # trusted module views
      extensions/
        examples/hello-world/ # separate build/package, never linked into host
      docs/

During Stage 00 keep the current root frontend paths and pnpm workflow. Stage 01 may move frontend into frontend/ once the web build is stable. Keep a single Go module for the host until a real independent SDK packaging consumer requires more. The example extension must be buildable/packageable independently; Go SDK distribution/module boundaries may be finalized then. Do not perform bulk moves merely for aesthetics.

## Runtime and lifecycle

- The host starts core facilities, initializes modules in dependency order, registers module contributions and starts the workbench; external extensions are discovered after registry availability.
- Each module has explicit identity, dependencies, start/stop or equivalent lifecycle and owner-scoped registration cleanup. Duplicate IDs, missing dependencies and dependency cycles fail before partial activation. Roll back on initialization failures; stop in reverse dependency order.
- The extension host is responsible for discovering packages, validation, activation, deactivation, supervision and cleanup. A failed extension must not prevent the host UI from opening.
- Use context cancellation, bounded queues, timeouts and explicit ownership for background work. Avoid global mutable service locators and implicit init side effects.
- Wails methods expose a small trusted frontend bridge for settings, commands, search, notifications, logs, contributions and extension management. Do not add Wails methods for each extension or expose privileged bindings to untrusted UI.

## Persistence and events

Use XDG-compliant paths on Linux; separate config, data, cache and runtime/log files as appropriate. Use SQLite where structured persistence is justified; store module settings and workspace layout with a schema version. Extension-owned data resides under a validated per-extension directory. Never put tokens in browser localStorage, log output or workspace state. Secrets use the OS keyring or another explicitly documented safe mechanism; if unavailable, do not silently downgrade to plaintext. MVP may omit secrets implementation until required.

Core events use typed envelopes and namespaced topics. Event payloads are bounded and must not broadcast secret data. Subscription cleanup is tied to owner lifecycle. Keep domain stores independent. The extension host mediates capabilities rather than giving an extension a direct database or registry reference.

## Existing Slicer extraction map

| Current path | Reuse/adapt | Remove coupling |
| --- | --- | --- |
| src/lib/tab.ts | IDs, positions, activation, close, pin and dynamic definitions | Entry, class, Java, scripts, welcome reinsertion and domain-specific persistence |
| src/lib/state.ts | Versioned layout/preferences pattern | Java/decompiler/JDK state, remote-share URL fields |
| src/lib/components/content.svelte | PaneForge layout | Domain PaneAccess and fixed assumptions |
| src/lib/components/pane/pane.svelte | Tab headers, reorder, collapse and close modes | Entry copying, tree highlighting, global event handler |
| src/lib/components/pane/header/item.svelte | Tab header and context menu | Domain icon adapters; nested control/accessibility issues |
| src/lib/components/pane/root.svelte | Async view resolution | Fixed Java tab types and ScriptTab fallback |
| src/lib/components/command.svelte and src/lib/shortcut.ts | Searchable palette, keyboard routing | JDK/workspace search and decompiler actions |
| src/lib/script.ts and script/ | Dynamic registration concepts | Remote script execution and existing domain SDK |

Slicer keeps all open pane content mounted and hides inactive panes; do not inherit this blindly. It persists only selected built-in tab types, not arbitrary documents. Its drag-and-drop and pane model do not by themselves implement arbitrary editor split groups. Adapt and test behavior rather than claiming it exists.

## Performance and observability

Lazy-load trusted views, avoid mounting inactive heavy views unnecessarily, retain essential editor state explicitly, virtualize long lists if needed, and avoid delayed hover transitions. Logs must be bounded, actionable and redacted. Measure startup and tab switching after the native shell exists; establish a baseline before introducing numerical performance targets.
