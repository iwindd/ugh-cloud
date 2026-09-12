# External skill category mapping

Use the skill's trigger and primary output to choose a Hermes category. Do not copy a publisher's directory name blindly.

| Source role | Hermes category | Typical examples |
|---|---|---|
| Engineering design, implementation, debugging, testing, code review, repository setup, git utilities | `software-development` | `tdd`, `code-review`, `diagnosing-bugs`, `setup-pre-commit` |
| Planning, interviewing, writing, teaching, handoff, asynchronous decision support | `productivity` | `grill-me`, `writing-for-agents`, `teach`, `handoff` |
| Media, email, notes, creative work, or other specialized domains | Existing matching Hermes category | Use the closest established class; do not create a new category for one imported skill |

When a source collection has `engineering`, `productivity`, `in-progress`, and `misc` directories:

- Map `engineering/*` to `software-development`.
- Map `productivity/*` to `productivity`.
- Map `in-progress/*` according to the skill's behavior: implementation and repository tooling to `software-development`; writing, handoff, and general workflow to `productivity`.
- Map `misc/*` according to the skill's behavior; most code/test/repository utilities belong in `software-development`.

After copying, compare the source skill count with the target `SKILL.md` count. A category may contain pre-existing skills, so validate by names rather than relying on the total alone.
