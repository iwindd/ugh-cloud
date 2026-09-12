---
name: hermes-plugin-development
description: "Use when building Hermes custom plugins. Verify the result."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [hermes, plugins, custom-tools, mcp, extensions]
---

# Hermes Plugin Development

Use this skill for a Hermes custom tool, plugin, slash command, hook, or external integration. Keep user-specific extensions outside Hermes core unless the user explicitly wants an upstream contribution.

## Procedure

1. Classify the request before writing files:
   - Use a skill when the change is knowledge or workflow guidance only.
   - Use a plugin for a local Python tool, slash command, hook, persistent plugin state, bundled skills, or several related capabilities.
   - Use MCP when the capability already exists as an MCP server, belongs in another language/process, or should be independently deployable.
   - Modify Hermes core only for an upstream-quality built-in feature.
2. Check the current official docs index and the matching Hermes reference before relying on API names. Prefer the live docs pages for plugin manifests, registration, MCP configuration, and validation commands.
3. If the design is ambiguous, run a design interview before implementation. Ask the whole current decision frontier in one round, give a recommendation for each question, and wait for confirmation before creating the extension. Do not silently turn a request for a slash command into an automatic behavior change.
4. Keep the extension profile-safe. Resolve the active Hermes home through `$HERMES_HOME` or `get_hermes_home()`; never assume another profile's `~/.hermes` directory.
5. For skill-backup plugins, make synchronization explicit and reviewable: expose the user-invoked `/ugh skill upload <skill-id> [--to-agent <agent-name>]` and `/ugh skill upload all [--to-agent <agent-name>]`; read source skills only from the active Hermes profile; use `--to-agent` to choose a logical destination agent even when it has no local profile; preserve each skill's category and complete directory contents (`SKILL.md`, `references/`, `templates/`, `scripts/`, and `assets/`); and create one GitHub pull request per changed skill, even when `all` is used. Keep curated/shared skills under `/skills/` separate from per-agent snapshots under `/agents/<agent-name>/skills/`; upload only the latter. During `all`, add or patch skills sourced from the active profile but leave target-only skills untouched. Treat explicit removals as deletion pull requests, never direct remote deletes; isolate managed files under the target agent namespace. Reuse an open PR branch for the same `(repository, target-agent, category, skill-id)` identity, but keep same-named skills for different target agents in separate branches and PRs. Use the target agent display name in PR titles, and record the source profile in the PR body.
6. Implement a plugin with a manifest and an importable `register(ctx)` entry point. Register each tool with a precise JSON schema, a toolset, and a handler. Make handlers accept `**kwargs`, return JSON strings, and convert expected failures into `{"error": "..."}` results.
7. For GitHub skill backup, make PR identity and idempotency deterministic: use `(repository, target-agent, category, skill-id)`, target `main`, name the branch `ugh-cloud/agents/<agent>/skills/<category>/<skill-id>`, and use titles `[Agent] Request to add|patch|remove skill \`<skill-id>\``. Compare Git blob SHAs, not raw local bytes against remote SHAs; unchanged content is a successful no-op, an open matching PR is updated, and merged/closed PRs require a new branch. If the repository is empty, bootstrap its default branch through the Contents API with a minimal placeholder commit before creating skill branches; Git tree creation alone cannot initialize an empty GitHub repository. In batch mode, isolate each skill's failure and return per-skill results instead of aborting the batch.
8. Gate optional capabilities with an availability check or declared environment requirements. Keep secrets in the profile secret mechanism or `.env`, not in source, manifests, or ordinary config.
9. For MCP, configure a named server under `mcp_servers`, scope filesystem/repository access narrowly, and use `tools.include` plus disabled prompts/resources when the server is sensitive or broad.
10. Validate before claiming completion: run the plugin doctor for local plugins, inspect `hermes plugins list`, restart or reload MCP as appropriate, and exercise the tool through a real Hermes invocation. A file that imports successfully is not proof that the agent can see or call the tool.
11. Report only what was actually verified, including the extension path, exposed command/tool name, verification command and result, and any untested external credential or service dependency.

## Standing pitfalls

- Prefer a plugin over editing `tools/` and `toolsets.py` for personal extensions because core edits are overwritten or conflict during upgrades.
- Do not expose a registered tool until it is included in the intended toolset; registration alone does not make it available to the agent.
- Keep tool descriptions specific about when and how to use the tool because vague schemas produce unreliable tool selection.
- Do not return Python dicts from plugin handlers; Hermes expects JSON strings at the tool boundary.
- Do not let optional imports or missing credentials prevent the whole plugin set from loading; gate or lazily import them instead.
- Do not enable a destructive or customer-facing MCP server without an allowlist; discovery can expose more operations than the workflow needs.
- Make explicit skill-upload scope override excludes only through a per-command `--force` confirmation; do not make force alter the persistent exclude policy.
- If `--force` is allowed to override secret warnings for this user's workflow, display the exact suspicious files and require explicit confirmation before including them; never hide the warning or place credentials in ordinary configuration/source files.
- Treat plugin discovery, tool registration, and tool invocation as separate checks; passing one does not establish the others.

See `references/plugin-layout.md` for the minimal plugin skeleton and the verification checklist. Use the official Hermes docs for API changes beyond that baseline.
