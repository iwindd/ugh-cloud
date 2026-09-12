---
name: local-filesystem-web-apps
description: Use for local filesystem web apps. Verify scans.
---

# Local Filesystem Web Apps

Use this workflow for browser applications that inspect local directories while keeping the UI read-only.

## Procedure

1. Inspect the target directory and existing context files before designing the data model. Identify the root directory, child collections, required marker files, and any hidden/system directories that should not appear as user-facing records.
2. Define a local API boundary for directory discovery, collection scanning, and on-demand text-file reads. Keep filesystem code in server-only modules; expose JSON records with stable IDs, relative paths, status, metadata, and supporting-file lists.
3. Resolve and validate every requested path on the server. Permit reads only inside the selected root, reject traversal and unsupported file types, and enforce a size limit before reading content.
4. Make the setup flow explicit: accept a user-selected root, discover available profiles/environments, show their counts and readiness, and require a profile selection before rendering the explorer.
5. Keep the explorer read-only unless editing is explicitly specified. Use a small client state store for selected record, filters, viewport, and persisted layout state.
6. Verify the real path, not only the TypeScript build: call the discovery and scan endpoints against a known directory, test an invalid root and traversal attempt, load the browser, select a record, and inspect the browser console.

## Standing rules

- Keep context and agent-facing files in English when the user requests it; localize product UI copy independently without changing source identifiers, paths, code, or source markdown.
- Prefer explicit refresh/rescan behavior over silent filesystem watchers for an explorer MVP, because it keeps content and layout changes predictable while reading.
- Report changed files, real verification results, and limitations concisely after implementation.

## Pitfalls

- Never import a server filesystem module into client code just to share a constant; bundlers may externalize Node APIs into the browser and make a successful build misleading. Put shared literals in a browser-safe module.
- Do not expose an arbitrary file-read endpoint without checking the canonical resolved path against the selected root; URL decoding and path normalization otherwise create a traversal boundary.
- Do not treat every directory as a domain record; require the agreed marker file or structure, otherwise caches and internal folders pollute the explorer.
- Do not load every supporting file during the initial scan; list files recursively and fetch text content on demand to keep startup responsive and avoid unnecessary binary reads.
