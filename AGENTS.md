# Agent Development Guide

Repository: `XIAN-SHENG-576692/my-agent-skills`. Distributes reusable skills for AI assistants.

## Before editing

- Read the affected skills and any nested agent guidance.
- For packaging or release changes, read [.github/workflows/release.yml](.github/workflows/release.yml), the source of truth for validation and ZIP contents.

## Documentation

- Write in English and use relative links for local files.
- Keep [README.md](README.md) user-facing: available skills, benefits, installation, and usage. Keep development guidance here.
- Update the README when capabilities change; describe actual behavior and published downloads accurately.

## Developing skills

- Keep each skill self-contained in `skills/<skill-name>/`.
- Give `SKILL.md` YAML frontmatter a lowercase, hyphen-separated `name` matching the directory and a nonempty `description` stating the task and when to use it.
- Specify inputs, outputs, missing-information handling, and clear completion criteria.
- Keep core instructions concise. Link to supporting files relative to the skill directory and state when to read them. Include only necessary scripts, references, and assets; use synthetic sample data.
- Keep optional `agents/openai.yaml` metadata consistent with the skill.
- Preserve established output and language conventions unless asked to change them; document language defaults and overrides.

## Verification

- Run `git diff --check`; verify documentation links, commands, and claims against repository contents.
- For skill changes, exercise representative requests and relevant edge cases against the stated outputs. Structural validation alone does not establish instruction quality.
- For code changes, run checks covering the affected behavior and failure modes.
- For packaging changes, run the workflow's validation and packaging logic locally without publishing. Inspect ZIP contents and keep generated archives in ignored `dist/`.

## Handoff and releases

Report changes, checks performed, and limitations, distinguishing structural checks from skill execution.

Tags matching `v*` trigger publication. Create or push release tags only when requested.
