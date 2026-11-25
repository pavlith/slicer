# Implementation roadmap

All stages are PLANNED at initial documentation commit. Execute sequentially in small coherent commits. Do not implement future stages early. Update this file with status, commit reference and actual validation evidence after each stage. If a check cannot run, record why and what remains; do not mark it passed. See [CODEX.md](CODEX.md).

| Stage | Scope | Required acceptance |
| --- | --- | --- |
| 00 | Slicer workbench extraction | Domain-neutral web shell builds; no active Java/reverse-engineering/script loader code; reusable panes/tabs retained |
| 01 | Go + Wails 3 host | Linux native dev/build; embedded frontend; clean start/stop; paths and minimal typed host bridge |
| 02 | Seven-region workbench | All named regions; hide/resize; safe persisted layout; neutral shadcn theme |
| 03 | Tabs/editor groups | Preview, pin, close, reopen, reorder, dirty protection, bounded split, restore and lazy registry resolution |
| 04 | Module lifecycle and registry | Explicit dependencies/start/stop/cleanup, duplicate/cycle handling, no domain coupling |
| 05 | settings, notify, logger | Validated settings persistence; notifications and output panel; zero extensions required |
| 06 | action, search, palette | Commands/shortcuts, context conflicts, provider cancellation and empty/error states |
| 07 | Contribution registry | Owner-scoped views/editors/status/actions; test module can register new types without shell changes |
| 08 | SDK and extension host | Versioned manifest/protocol, local discovery/activation, lifecycle/permissions, isolated UI spike |
| 09 | Independent Hello World extension | Separate packaging/install, view and editor tab, action/search/log/notify, disable/re-enable/uninstall, failure isolation |
| 10 | Core 0.0.1 | Linux build + native smoke; empty and installed profiles; docs, license notices, known limitations |
| 11 | Mail extension migration | Separate extension using Milo donor; no mail imports in Core; mail data/security migration plan |
| 12 | Marketplace and public distribution | Actual isolation/trust policy, package integrity, publisher validation, update rollback and registry UI |

Stages 00–10 are Core MVP. Stages 11–12 are follow-up work; marketplace may be sequenced after a private/trusted Mail prototype, but public third-party packages are gated on security. Contacts/Calendar are subsequent independent extensions. Windows validation is a separate future milestone.

## Stage 00 detail — web workbench extraction

1. Inspect source tree and current package scripts. Record baseline pnpm install --frozen-lockfile, pnpm check, pnpm build results (or environment blocker). Preserve MIT and third-party notices.
2. Trace domain coupling using tab.ts, state.ts, app.svelte, content.svelte, pane/pane.svelte, pane/root.svelte, command.svelte and shortcut.ts. Extract generic tab state and view resolution first; introduce simple neutral placeholder views only.
3. Remove the app's Java-specific menus, workspace/decompilers, JDK, bytecode graph/search, remote-file/share and dynamic script execution from the live entry and dependency graph. Remove unused domain source/assets/deps after confirming no imports. Keep shared UI and localization patterns; remove obsolete domain translation keys if safe.
4. Keep pane/layout and basic tab interactions working. Do not add Wails, native IPC, marketplace, real modules or extension execution yet. Root frontend paths and pnpm remain for this stage. Keep name slicer until owner decides.
5. Verify build/check, relevant tab tests (choose a lightweight existing-compatible runner if needed) and a browser smoke when possible. Update README only to describe current behavior and docs navigation; do not advertise future features as shipped.

## Cross-stage acceptance

No unused compatibility layers solely to keep the old domain alive. No unrelated dependency upgrades. New contracts get focused tests, especially lifecycle, persistence and error cases. Preserve working build at each stage, or document a reproducible pre-existing/environment issue. Test Linux runtime only when native host is available; do not claim Windows support without evidence. Record actual commands, results, important trade-offs and next stage. Keep commit boundaries meaningful and avoid force pushes.
