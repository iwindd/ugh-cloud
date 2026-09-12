---
name: filesystem-backed-web-apps
description: Use when building filesystem-backed web apps. Verify safely.
---

# Filesystem-Backed Web Apps

Use this workflow when a browser UI must explore or read local files through a development server or local backend.

## Procedure

1. Inspect the repository and the target filesystem before choosing the data model. Identify the root boundary, descriptor files, nested support directories, and malformed entries.
2. Separate server-only filesystem code from browser code. Keep `node:fs`, `node:path`, and other Node imports behind an API boundary; place shared browser-safe constants in a client module so Vite cannot pull Node modules into the browser bundle.
3. Implement a narrow read API with explicit source and file parameters. Resolve paths, enforce that every requested file remains inside the selected root, restrict text reads by extension and size, and return actionable errors.
4. Parse the descriptor file into a tolerant domain model. Preserve valid entries when one entry is malformed; represent malformed entries with warning/error status instead of failing the entire scan.
5. Recurse through nested support directories when listing related files, but exclude the primary descriptor file from that list. Load supporting files on demand rather than preloading everything.
6. Build the UI around the domain model: show the hierarchy in the main canvas, keep the initial detail panel empty with an explicit selection prompt, and render selected markdown only after sanitization.
7. Persist only user navigation state such as source path, node positions, and viewport. Keep the explorer read-only unless write semantics, validation, backup, and undo are explicitly designed.
8. Verify in layers: run typechecking, run the production build, start the dev server, call the real scan endpoint, test a traversal request, exercise search/filter and selection/drawer behavior in a browser, and inspect browser console errors.

## Standing pitfalls

- Do not import server filesystem modules into UI components; Vite will externalize Node modules into the browser bundle and produce a build that may appear successful but cannot run correctly in the browser.
- Do not treat a successful directory scan as proof of file-read safety; test a path outside the selected root because traversal bugs live on the second endpoint.
- Do not list the descriptor file as a supporting file; it duplicates the main detail view and makes file counts misleading.
- Do not space hierarchy nodes with a fixed per-category offset when child counts vary; derive subsequent category positions from the rendered child count or nodes will overlap.
- Do not stop at a successful build for an interactive app; browser-level verification catches event wiring, empty-state, and rendering failures that typechecking cannot.
