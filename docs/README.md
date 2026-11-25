# Slicer — Desktop Workbench platform

Status: proposed target architecture, not current functionality. Baseline: existing Slicer frontend at commit 3fb8d07f4fcd0120ff278505ae3a8c45606133d4. The repository name stays slicer until the owner decides otherwise.

## Objective

Turn Slicer into a reusable Linux-first desktop application foundation, not a Java reverse-engineering product and not a mail client. Preserve reusable workbench mechanics, remove domain-specific code in planned stages, introduce Go + Wails 3, and prove the platform with a separately installable Hello World extension. Mail will be a subsequent extension using the existing Milo project as donor.

## Reading order for Codex

1. [VISION.md](VISION.md): scope, terminology, decisions and non-goals.
2. [ARCHITECTURE.md](ARCHITECTURE.md): repository structure, boundaries, runtime and modules.
3. [WORKBENCH.md](WORKBENCH.md): seven UI regions, tabs and contributions.
4. [MODULES.md](MODULES.md): built-in service responsibilities and contracts.
5. [EXTENSIONS.md](EXTENSIONS.md): SDK, manifests, lifecycle, isolation and future packaging.
6. [ROADMAP.md](ROADMAP.md): ordered stages and acceptance criteria.
7. [CODEX.md](CODEX.md): execution rules and a ready-to-run Stage 00 task.

These documents define target behavior. Do not describe a feature as implemented until verified. Changes to these contracts must be documented together with implementation. ROADMAP.md is the progress source of truth; all stages begin as planned. Existing README.md and application code are intentionally unchanged by the initial documentation commit.

## Core MVP definition

A native Linux window with the seven-region workbench, reusable tabs, the built-in modules settings, notify, logger, search and action, a contribution registry, and an extension host supporting an independently installed local demonstration extension. Storage, secrets, events and extension management are minimal platform facilities, not new product domains. No mail, Java or marketplace dependency is needed to run the foundation.
