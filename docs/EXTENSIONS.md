# Extension SDK and host

Status: target design. MVP supports a local, explicitly trusted developer extension. Public untrusted extension execution and marketplace are NOT part of Core MVP.

## Package and manifest

Extensions are separate installable packages, never compiled into the host. First support an unpacked directory installed by an explicit development action. Later a .slicerext archive may wrap the same layout; the final filename is provisional.

    hello-world/
      manifest.json
      backend/linux-amd64/hello-world   # optional, executable
      frontend/index.html               # optional, prebuilt isolated UI
      frontend/assets/
      README.md
      LICENSE

Illustrative manifest (versioned schema to implement with tests):

    {
      "id": "example.hello",
      "name": "Hello World",
      "version": "0.0.1",
      "apiVersion": "1",
      "entrypoints": {
        "backend": {"linux-amd64": "backend/linux-amd64/hello-world"},
        "frontend": "frontend/index.html"
      },
      "permissions": ["notify.publish", "logger.write", "action.register"],
      "contributes": {
        "activities": [{"id": "example.hello.activity", "title": "Hello", "icon": "box"}],
        "views": [{"id": "example.hello.navigation", "region": "primary", "activity": "example.hello.activity"}],
        "editors": [{"id": "example.hello.editor", "title": "Hello"}],
        "actions": [{"id": "example.hello.open", "title": "Open Hello"}],
        "statusItems": [{"id": "example.hello.status", "region": "footer"}]
      }
    }

IDs are namespaced, unique and owned by the manifest identity. Validate API/platform compatibility, allowed regions, entrypoints, manifest size and permissions before starting anything. Reject absolute/traversal paths, escaping symlinks, duplicates and unsupported entrypoints. Treat package metadata as data, not executable HTML. Use immutable installed versions and preserve data on disable/update; removal of data is separate explicit user intent.

## Lifecycle and protocol

discover -> validate -> installed/disabled -> activate -> active -> deactivate -> disabled. Incompatible/failed states expose a diagnostic. MVP activation may be explicit; lazy triggers later. A package can provide frontend-only or backend-only contributions. Activation failure rolls back registrations and terminates any started process. Deactivation removes views/actions/search/status items and event subscriptions, handles dirty editor state, cancels work and stops owned processes. Use bounded graceful shutdown and then termination. Never let one failure take down the host.

Prefer a supervised subprocess for Go extensions, not plugin.Open, shared Go ABI, or host-side imports. Proposed IPC: JSON-RPC 2.0 over dedicated stdio (or local transport if justified by Wails/platform constraints); no exposed TCP server by default. Frame messages explicitly, negotiate API version and session identity, separate protocol output from diagnostic output, enforce max message size, timeouts and cancellation. The host derives owner identity from the connection; do not trust owner IDs passed by callers. Define explicit stable error codes: incompatible, permission denied, invalid arguments, unavailable, cancelled, internal. Test malformed messages and process exit.

SDK Go and TypeScript clients expose only supported services and contribution registration. The host validates capability grants per call. The SDK must be usable without importing internal host packages. Keep schema examples versioned; do not promise backwards compatibility beyond the documented API version.

## UI and security boundary

Trusted modules may mount local Svelte components. External extension UI is separately built and rendered in an isolated context, initially a sandboxed frame or separate webview as supported by the pinned Wails implementation. Verify origin/sandbox behavior in a native spike before committing to a transport. No privileged Wails bindings, shared host DOM, arbitrary remote imports, or direct access to host secrets from extension UI. A narrow validated message bridge should authorize calls against the installed extension/session and use explicit message types. Do not use wildcard origins when origin authentication is required; opaque origins need a dedicated authenticated channel and source checks. The host retains control of chrome, prompts, permissions and navigation. Pass theme tokens via a constrained bridge; extension frontend may use shadcn-svelte but is not entitled to import host-private components.

Important: an independent native subprocess running as the same OS user is NOT an OS sandbox; it may still access files/network outside SDK controls. MVP must label such packages trusted developer code, require explicit user installation/activation and not claim strong isolation. Before public third-party distribution, implement and test an actual OS capability sandbox or a constrained runtime and address supply chain trust, signatures, update rollback and revocation. Avoid loading arbitrary remote JS into the Wails WebView. The old Slicer script loader is not an acceptable trust boundary.

## Local installation and future marketplace

For MVP, choose a local package directory, validate, stage in an owner/version-specific directory, atomically register, and activate only with explicit consent. For uninstall, stop first and remove package artifacts; retain extension-owned data unless separately requested. Use no hidden network fetches. Failed install must leave no active partial state. Maintain version/API compatibility metadata and allow reinstall.

Later, use a registry of signed/versioned artifacts, possibly hosted on GitHub Releases initially. Download to staging, check integrity and publisher policy, inspect archive limits/path traversal, prompt for changed permissions, and atomically update with rollback. A checksum alone is not publisher authentication. Marketplace UI and automatic updates are separate stages, gated on the security requirements above.

## Mail as a future extension

Existing pavlith/milo is a donor only. Mail owns IMAP, SMTP, OAuth provider details, accounts, mail sync, message/thread storage and UI. The host has no Mail-specific methods or imports. Mail contributes folder navigation in primary, message/inbox/compose editor types, connection status in footer, sync output in panel and search/actions. Use opaque resource IDs; preserve message/draft data and security boundaries. Contacts and Calendar remain separate extensions. Do not migrate mail until the independent Hello World proof passes.
