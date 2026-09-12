---
name: installing-external-skills
description: "Use when installing external skills into Hermes. Organize."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [windows, macos, linux]
metadata:
  hermes:
    tags: [hermes, skills, installation, profiles, organization]
---

# Installing External Skills into Hermes

Install external skill collections into the active Hermes profile, preserve their support files, organize them by task class, and verify that Hermes can load them.

## Procedure

1. Identify the active profile before writing anything. Resolve `$HERMES_HOME`; never assume `~/.hermes` when a profile is active. Treat `$HERMES_HOME/skills/` as the target library.

2. Read the publisher's installation instructions and identify the canonical source repository. Prefer the repository named by the publisher over a copied web page or a third-party mirror. Record the source commit after fetching it.

3. Discover the collection before installing it. Use the publisher's installer with its listing mode, or inspect the repository's `skills/` tree. Count the discovered `SKILL.md` files and note the source category of every skill.

4. Try the supported installer only when its destination is compatible with Hermes. For the Skills CLI, the canonical package form is `npx skills@latest add <owner>/<repo>`. Use `--all` only when the user wants the complete collection, and use `--copy` when editable, self-contained files are preferred over links.

5. If the installer reports that global installation is unsupported or places files outside the active Hermes profile, fetch the canonical repository and copy each skill directory into `$HERMES_HOME/skills/<Hermes category>/<skill-name>/`. Preserve `SKILL.md`, `references/`, `templates/`, `scripts/`, and other support files; do not flatten a skill into one file.

6. Map source categories to Hermes categories by task class, not by publisher directory name. Put engineering workflows and code utilities under `software-development`; put general planning, writing, teaching, and handoff workflows under `productivity`. Consult `references/category-mapping.md` for the default mapping and adjust only when the skill's actual trigger warrants it.

7. Before deleting unscoped or duplicate manifests, confirm the categorized copy exists and is complete. Remove only files created by the current installation; never delete bundled, hub-installed, pinned, externally owned, or user-owned skills. Leave unrelated directories untouched.

8. Verify the result with `hermes skills list`. Confirm every intended skill is enabled, the count matches the discovered collection plus pre-existing local skills, and no duplicate name makes lookup ambiguous. Use `hermes doctor` only as a secondary profile/directory check; do not treat unrelated credential or package warnings as installation failures.

9. Report the target profile path, source repository and commit, number installed, category placement, and any unresolved cleanup or installer limitation plainly. Do not claim success from a command exit code alone; base the report on the final Hermes listing.

## Durable pitfalls

- Use the active `$HERMES_HOME`, not the default profile path — Hermes profiles isolate their skills and a correct install in another profile is invisible here.
- Preserve the whole skill directory — support files hold procedures and templates that `SKILL.md` references at runtime.
- Organize by task class and keep one canonical copy — unscoped duplicates create ambiguous skill resolution and make future updates target the wrong file.
- Treat installer summaries as provisional — an installer can clone a repository while failing to link or register files for a specific agent, so verify with Hermes itself.
- Keep source provenance beside the installation notes or report — a commit identifies exactly which external instructions were imported when the upstream collection changes.

A deeper source-to-Hermes category decision table is in `references/category-mapping.md`.
