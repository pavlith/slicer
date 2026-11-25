# Product vision and decisions

## Purpose

Build a reusable desktop workbench for domain applications. An empty installation is useful for inspecting settings, logs, notifications, actions and installed extensions, but has no mail-specific or reverse-engineering-specific functionality. Extensions turn it into a mail client, CRM, task manager or other desktop application. Mail is an extension even when distributed by the same publisher.

## Terminology (normative)

| Term | Meaning |
| --- | --- |
| Core | Minimal platform bootstrap, lifecycle, service registry, platform paths, IPC and extension supervision. |
| Module | Trusted built-in system service compiled with the host; may contribute UI. Not an extension. |
| Extension | Independently packaged domain functionality; installed and activated separately. No built-in/external distinction. |
| Workbench | Domain-neutral Svelte shell, layout, view registry and tab management. |
| Contribution | Owned registration of a view, editor type, command, search provider, status item or other UI integration. |
| View | A registered UI surface in a specific region. |
| Tab | An instance of an editor/view with stable identity, state and lifecycle. |
| Action | Named operation registered through the action module, invoked by menus, shortcuts, palette or code. |
| SDK | Versioned, public extension-facing contracts, schemas and client libraries. |
| Host | Runtime that validates, activates and supervises extensions and mediates their access. |

## Architectural decisions

- Use current Slicer as the starting repository and workbench donor. Do not start over from Milo or copy all of Milo into Core.
- Retain the reusable Svelte 5 + TypeScript + Vite + Tailwind CSS 4 + shadcn-svelte stack and pnpm lockfile. Add Go + Wails 3 for desktop. Select compatible pinned versions when the relevant stage is implemented.
- UI resembles VS Code in structure and interactions, not a pixel-for-pixel clone. Use standard shadcn-svelte semantic tokens, neutral Zinc defaults, Lucide icons, Mode Watcher and PaneForge. No IBM Carbon theme, tokens or assets.
- Keep the existing MIT license and copyright notices, including relevant upstream notices. Remove unused dependency code only after checking imports and licenses.
- Linux-first; Windows portability is desirable but validation and packaging are deferred. No Flatpak, macOS, mobile or browser/PWA release obligations for MVP.
- Keep Core free of domain concepts, credentials specific to providers and mail/Java data models. The Wails service is a composition boundary, not a giant domain API.
- No arbitrary remote JavaScript execution in the privileged host. The old Slicer script runtime is a design reference only, not the extension security model.
- Prefer small working vertical slices and explicit contracts over speculative abstractions. Do not create empty packages merely to match a directory diagram.

## Out of scope for Core MVP

Mail/IMAP/SMTP, calendar, contacts, Java analysis, terminal emulation, collaborative workspaces, account sync, cloud marketplace, automatic remote updates, advanced docking and public untrusted extension execution. Marketplace and mail migration are separate later milestones. Existing Milo remains unchanged by this migration unless expressly requested.

## Open decisions

Final product/repository naming, public distribution format, cross-platform packaging, publisher trust policy and a general-purpose untrusted extension sandbox are deliberately not finalized. The roadmap chooses a local trusted development extension and an IPC/isolated UI architecture as an MVP starting point; do not confuse this with a security guarantee.
