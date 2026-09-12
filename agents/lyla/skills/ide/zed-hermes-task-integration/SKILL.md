---
name: zed-hermes-task-integration
description: Use when creating Zed tasks that invoke Hermes.
version: 0.1.0
author: iwindd, Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    editorial_name: Zed and Hermes Task Integration
    editorial_description: Create and verify global Zed tasks that pass the active file to a selected Hermes profile.
    tags: [zed, hermes, tasks, automation, editor-integration]
---

# Zed and Hermes Task Integration

Create a global Zed task that invokes a selected Hermes profile with the file currently open in Zed. Use this for repeatable editor-to-agent workflows such as turning a skill into a diagram. The task should pass a file path and a precise prompt; it should not silently modify the source file or embed secrets.

## When to Use

Use when the user wants a Zed Command Palette task that sends the current file or selection to Hermes.

Do not use for:

- Zed keybindings that do not run a command.
- Project-local tasks when the user explicitly wants a global task.
- Direct edits to a skill or source file without a separate user request.

## Prerequisites

- Zed is installed and its version is known through `terminal` when schema compatibility matters.
- Hermes CLI is available on PATH, or its executable path has been explicitly provided.
- The target Hermes profile is known; use an explicit profile flag rather than relying on the default profile.
- The task input file is saved before execution if Hermes must read on-disk content.

## Procedure

### 1. Discover the task configuration

Check the installed Zed version and locate the global task file using official Zed documentation and platform conventions. Prefer opening/editing tasks through Zed's `zed: open tasks` action when the user can do so; if writing through tools, verify the resolved path first.

Do not assume the current schema from a different Zed release. Some Zed versions use a root array of task objects, while other documented examples may show versioned task containers. Inspect the user's existing file or the installed version's schema before writing.

**Completion check:** the exact global task path and accepted root JSON shape are known.

### 2. Define the task contract

Use a descriptive label that the user can find in `task: spawn`, for example:

```text
Lyla: Make this skill into the diagram
```

Use Zed task variables rather than hard-coded source paths:

- `$ZED_FILE`: absolute path of the current file.
- `$ZED_WORKTREE_ROOT`: current worktree root for command working directory.
- `$ZED_SELECTED_TEXT`: selected text when the task is intentionally selection-based.

For a file-based workflow, tell Hermes to read `$ZED_FILE`. Ask it to report created artifact paths and preserve the source unless modification was explicitly requested.

**Completion check:** the task has a label, dynamic input variable, explicit Hermes profile, output behavior, and source-preservation rule.

### 3. Route to Hermes explicitly

Invoke Hermes with the selected profile before the `chat` subcommand, and pass the worktree as the session context. A typical command shape is:

```text
hermes --in "$ZED_WORKTREE_ROOT" -p <profile> chat -q "Read the skill file at $ZED_FILE and ..."
```

Keep the prompt short enough for shell quoting and make artifact expectations explicit. Never include passwords, API keys, tokens, private keys, or raw secret values in the task command.

Use a new terminal and reveal its output when the user needs to see completion. The Zed task terminal is the observable result channel; it does not silently inject a response into an already-open desktop chat unless a separate integration is configured.

**Completion check:** the command invokes the intended Hermes profile and exposes its output in a visible Zed terminal.

### 4. Write the global task

Preserve any existing tasks. Add one task object with:

- `label`
- `command`
- `cwd` set to `$ZED_WORKTREE_ROOT` when appropriate
- `use_new_terminal` when isolated output is desired
- `reveal` set to show the result when completion visibility matters

Use the root shape accepted by the installed Zed version. Validate JSON after writing; do not add comments to strict JSON.

**Completion check:** the file parses as JSON and contains exactly the requested task without deleting unrelated tasks.

### 5. Verify through the UI

After writing or editing the global task file:

1. Save the source file that Hermes will read.
2. Reload Zed or reopen the task configuration if the task list is stale.
3. Press `Ctrl+Shift+P` on Windows/Linux or `Cmd+Shift+P` on macOS.
4. Run `task: spawn`.
5. Search for the exact task label.
6. Run it and confirm the Hermes profile and file path in the terminal output.

If the picker says no matches or reports an array/object type error, stop and re-check the root task schema for the installed Zed version before changing the command.

**Completion check:** the task is visible in `task: spawn` and a test run produces visible Hermes output.

## Pitfalls

- Match the root JSON shape to the installed Zed version; a valid-looking versioned object is rejected when that version expects an array, so the task disappears from the picker.
- Use `$ZED_FILE` instead of hard-coding a skill path; hard-coded paths make a global task unusable for the next file.
- Pass `-p <profile>` explicitly; the `hermes` default profile may not be the profile named in the task label.
- Save the current editor file before running a file-path task; Hermes reads disk state, not unsaved editor-buffer state.
- Keep stdout behavior in mind when Hermes launches an MCP-related command; protocol output and diagnostics must not be mixed.
- Do not claim the task sends into the current desktop conversation; a shell task launches Hermes CLI and displays its result in Zed's terminal unless a separate bridge exists.
- Preserve unrelated task objects when editing a global task file; replacing the whole file can silently remove the user's other automation.
- Reload Zed after schema edits when the task picker is cached; stale UI state can look like a malformed task file.

## Verification

- [ ] Official Zed task documentation and installed version were checked.
- [ ] Global task path and JSON root shape were verified.
- [ ] Task label is visible in `task: spawn`.
- [ ] `$ZED_FILE` and `$ZED_WORKTREE_ROOT` are used where needed.
- [ ] Hermes profile selection is explicit.
- [ ] JSON parses without comments or schema errors.
- [ ] The test run visibly invokes Hermes and reports its output.
- [ ] No source file, credential, or unrelated task was modified.
