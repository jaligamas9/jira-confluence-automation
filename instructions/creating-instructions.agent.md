# Creating Instructions

- Keep reusable workflow knowledge in `./instructions/` as platform-agnostic Markdown.
- Name instruction files `[verb]-[operation].agent.md` with lowercase hyphen-separated words.
- Keep each instruction focused on one workflow and prefer concise, actionable bullet points.
- Use sub-bullets with `+` for keywords, target file globs, and exceptions.
- Add every instruction to `./instructions/main.agent.md` with a one-line description and useful Keywords.
- Use a skill under `./instructions/[name]/SKILL.md` when the workflow needs scripts, reference documents, or reusable assets.
- Give each skill `name`, `description`, and `version` frontmatter, and link it from the catalog.
- Keep instruction content independent of IDE-specific frontmatter and runtime settings.
- Use thin platform adapters for IDE integration:
  + VS Code prompt files belong in `./.github/prompts/` and use `.prompt.md`.
  + Cursor rules belong in `./.cursor/rules/` and use `.mdc`.
  + Claude commands belong in `./.claude/commands/` and use `.md`.
- Make the platform entry point reference `./instructions/main.agent.md` and reload it for every prompt.
- When updating an instruction, read it first, preserve useful content, and make targeted additions.
- Test practical examples before adding them to an instruction.
- Use English for instruction content and keep explanations shorter than the actionable steps.
- After setup, verify the entry point, catalog links, adapter paths, and IDE settings.