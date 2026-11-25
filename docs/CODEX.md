# Codex implementation guide

Start here for execution, then read README.md in this directory and the stage's linked specifications. These are project design instructions, not a claim of completed features. If a root AGENTS.md exists, follow it as well. The initial documentation task intentionally did not create a root AGENTS.md because the owner requested docs-only changes.

## Working rules

- Work only on the explicitly selected stage. Inspect current source and git status before editing. Preserve unrelated user changes and repository history.
- Keep main stable; use dev for implementation if the owner has created it or explicitly directs you to create it. If only main exists and branch instructions are absent, do not invent a new branch policy or push without authorization.
- Use existing pnpm and lockfile; pin compatible dependencies and regenerate lockfile only for intentional changes. For Go, use gofmt and focused go test once introduced. Use the existing shadcn-svelte component conventions; do not replace the design system wholesale.
- Prefer simple concrete implementation over premature generic frameworks. Remove dead domain code after checking usages; no broad rewrite of reusable components.
- Do not retain domain assumptions in Core, Workbench or Module APIs. Do not add Mail or Java stubs to the host.
- Write focused regression tests for changed behavior. Run existing check/build scripts and only claim commands actually executed. Use a browser/native smoke check when available; report unavailable tooling and pre-existing failures.
- No remote script execution in privileged UI, no secrets in logs or settings, no sandbox claims for same-user native processes.
- Update only relevant docs and ROADMAP.md at stage completion, with status/evidence. Commit in coherent slices if requested; never force push or modify branch protection. No sweeping formatting or unrelated upgrades.
- Keep token usage practical: read targeted files, use narrow tests while iterating, run the full appropriate checks at the end. Avoid excessive speculative audits. Do not skip essential security or functional checks.

## Ready-to-run prompt: Stage 00

Implement Stage 00 — Slicer Workbench Extraction in the current repository.

Read docs/README.md, docs/VISION.md, docs/ARCHITECTURE.md, docs/WORKBENCH.md, docs/ROADMAP.md and this document. Treat current Slicer as the starting frontend, not as a product to preserve. The target is a reusable domain-neutral desktop workbench; Go/Wails arrives in Stage 01.

Inspect the current root frontend and dependencies. Preserve and adapt generic tab/pane state, headers, drag-and-drop, resize, settings/layout persistence, command and shortcut patterns, UI primitives and the light/dark/system design. Remove Java-specific functionality and script execution from the live application and unused source/dependencies only after tracing imports. Replace the initial domain views with neutral demo/empty views. Use standard shadcn-svelte semantic theme; no Carbon. Keep all seven region identifiers in the target specification, but do not implement advanced layout or editor groups until their stages.

Keep the current pnpm build working, retain license notices and update current README to reflect the extraction. Do not migrate Milo/Mail, introduce native runtime, install packages from a marketplace or build a real extension host in this stage.

Acceptance: domain-neutral shell opens; tabs can open/focus/close, pin/reorder and basic panes resize/collapse as retained from the donor; no Java/decompiler or remote-script functionality in live bundle; pnpm check and pnpm build succeed or a documented reproducible blocker is reported. Add focused tests of extracted state if feasible. Report changed files, commands and actual results, remaining limitations and recommended next stage; update ROADMAP.md. Stop after Stage 00.

## Completion report format

- Stage and status (planned / in progress / completed / blocked).
- Summary of implementation and boundary decisions.
- Relevant commits and changed files.
- Verification table: command, result, blocker if any.
- Known limitations and next stage (do not implement it yet).
