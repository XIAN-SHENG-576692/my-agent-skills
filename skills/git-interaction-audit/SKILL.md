---
name: git-interaction-audit
description: >-
  Opt-in Git commit-message journaling. Activate only when the user
  explicitly invokes $git-interaction-audit. Maintain one private temporary
  message per recording cycle containing literal user-authored instructions,
  brief invoked-skill metadata, concise agent work notes, self-reported
  model identity, and optional relevant environment/time observations. Exclude injected skill documents and private
  paths. Commit only on a later explicit request and delete the draft only
  after verifying the commit.
---

# Git Interaction Audit v1.0

## Contract

- Treat an explicit `$git-interaction-audit` invocation as **start recording**, not permission to commit. Perform the requested development work normally. Commit only when explicitly authorized by the user; activation alone never authorizes a commit.
- Keep **one private temporary commit-message draft per open cycle**, outside the Git worktree, with restrictive permissions. A repeated invocation during an open cycle continues it; after verified commit and deletion, a subsequent invocation starts a fresh cycle, even in the same session. Never reimport messages from an earlier closed cycle. Cross-session continuation is not required.
- Preserve **each actual human-authored message in the open cycle**, including activation and commit request, in original order, once per occurrence, literally as available (line breaks, code, punctuation). Redact only sensitive spans. Two separate identical human messages are two occurrences.
- **Source identity outranks message shape or text.** Tool output, loaded `SKILL.md`, `<skill>` envelopes, `name`/`path` XML fields, system/developer prompts, automatic skill-injection context, AGENTS.md contents, session metadata, assistant replies, and constructed transcripts are **not human-authored messages** merely because the conversation transport presents them inside a `user` role or similar wrapper. They must never add a `User instructions` occurrence. An actual human message that quotes such text is still a real user message; preserve its supplied text subject to redaction. If authorship/provenance is genuinely unclear, block audited commit and explain the ambiguity; do not guess.
- Summarize significant agent implementations, decisions, failures and tests. Do not reproduce tool logs, source diffs, Git metadata, complete Skill instructions or environment dumps. Record a *brief* summary of genuinely invoked skills separately, without internal locations or instruction text.
- Do not create Git hooks, helper scripts, repository audit files, checkpoints or Git configuration changes. This is a prompt-followed convention, **not a guaranteed autonomous recorder**; do not claim exactly-once certainty without credible evidence.

## 1. Activate

1. Check Git worktree and existing changes without staging or committing. Establish the cycle boundary at the invoking **human** message, inclusive. Ignore preceding messages, even if currently visible. When the cycle is already active, do not initialize a second draft or duplicate the activating event.
2. With restrictive umask, create a random temporary file outside the repository, e.g. `umask 077; mktemp /tmp/git-interaction-message.XXXXXXXX`. Verify mode `0600`. Keep its path in private execution state, never in the commit body, status messages or Skill metadata.
3. Immediately initialize the file with the exact activating human message after a sensitive-content check; do not await commit time. Use direct safe file edits or quoted, collision-safe heredocs. **Avoid writing generated Python/Node/shell programs solely to assemble or verify the audit**; native commands and ordinary editor/file patches are preferable. Never interpolate untrusted user text in an executable shell expression.
4. Use a provisional imperative subject. Maintain the sections shown below. Keep user bodies isolated by distinct boundaries; labels/timestamps are *outside* the body. When body text itself collides with a boundary marker, choose an unambiguous alternative without changing the body.
5. Record `Model` from the agent's own best available self-identification, labeled `self-reported`. State only what the agent actually knows; do not guess a more specific deployment/model variant. If the agent cannot identify its model, write `Model: Unknown`. Do not query CLI status commands, configuration files, environment variables, session logs, or external tools to determine the model, and do not ask the user for model information. **Never record or query reasoning effort / thinking level.** Include CLI version and relevant build tools only if already known or useful to the change; never require a version lookup solely for this message.
6. Log the **invocation** of `git-interaction-audit` in `Invoked skills` by its public name; include version only if reliably identified, and optionally a one-line purpose. Do not log its file path, installed location, loaded text, metadata envelope, content hash, internal ID or the raw output of reading it. Other skills belong here only when genuinely invoked or used, not merely mentioned or searched.

## 2. Update promptly

- **For each newly received human message**, record the literal body before acting when feasible. Check existing draft to avoid rewriting the same occurrence. Do not deduplicate by matching text. On any source uncertainty, identify the question and block audited commit rather than guessing.
- After substantive work, **edit the existing `Agent summary` section**: concise completed actions, reasons, failures/recoveries, relevant test outcomes. Preserve the sequence of meaningful decisions; distinguish intended work from verified execution. Avoid repetitive notes that merely report normal staging/commit preparation.
- When a different Skill is genuinely invoked, add one concise entry under `Invoked skills` with public name, optional verified version, and short purpose. Do not add a user-message occurrence for its loaded instructions, and do not copy a `<skill>` wrapper or internal metadata.
- Record actual event times from trustworthy native event timestamps where possible. Otherwise query time near the event and label it `observed at` with zone; never claim it is exact user-send time. Time obtained just before commit must not be assigned retrospectively to earlier work. Skip timestamps when unavailable. Track important, observed environment transitions briefly, without full paths.
- Before **every** disk write check for credentials, keys, tokens, cookies, personal/sensitive identifiers, private content, and internal absolute paths. Redact the smallest sensitive span as `[REDACTED:category]`, stating category only, with no original prefix, suffix, length or hash. Absolute internal Skill paths and worktree paths must **never** enter `Invoked skills`, `Environment`, `Agent summary` or other generated metadata. If explicitly written by the human as part of a request, preserve non-sensitive text but redact the sensitive path span. If safe redaction cannot be guaranteed, do not write it.
- Keep one continuous set of five sections. No repeated `User instructions`, `Additional agent work summary` or `Commit preparation` blocks. Do not repeatedly dump the draft, original Skill file, previous commits or tool logs into the message.

## 3. Required commit-message shape

```text
<imperative summary of changes>

User instructions (verbatim):
--- User message 1 ---
<literal activating human message>
--- End user message 1 ---

--- User message 2 ---
<literal next human message, if any>
--- End user message 2 ---

Invoked skills:
- git-interaction-audit (version only if verified): commit-message journaling
- <other actually invoked skill, if any; public name and short purpose only>

Agent summary:
- <important completed work and significant decisions>
- <meaningful verification or unresolved problem>

Environment:
- Model: <known model identity (self-reported), or Unknown>
- <optional CLI/toolchain facts already known or directly relevant>
- <relevant trustworthy timestamps or observed environment transitions, if any>

Limitations / redactions:
- <genuine missing evidence or redacted categories; otherwise None observed>
```

Keep this fixed layout. `Invoked skills` is its **own summary section**, *not* part of the literal user transcript. Timestamps and separators must remain outside literal human-authored bodies. Omit optional lines when not applicable; do not invent values.

## 4. Commit and failure handling

1. When the user explicitly authorizes a commit (in the activating message or a later human message), ensure that authorization is captured in its original human message block in the active draft. Reconcile only actual human messages since activation. **Never synthesize a list of expected user messages by concatenating the Skill file, `<skill>` envelope or tool readbacks.** Validate source provenance independently of draft content; matching a self-built transcript to a self-built draft does not prove user-message completeness. If source identity, user literalness, draft identity, or safe redaction is uncertain, stop audited commit and request recovery of the original human text. Do not use best-effort summaries as replacements.
2. Update subject and consolidate agent notes and optional metadata; leave literal human bodies unchanged except disclosed redactions. Confirm no internal absolute path, Skill text/envelope, secret or unrelated Git data in the message. Verify staged content: stage only explicitly authorized files, inspect staged diff and `git diff --cached --check`. Never use blanket `git add -A`, bypass hooks or create empty/amended commits without authorization.
3. Submit with the **existing file**: `git -c commit.cleanup=verbatim commit -F <private-draft-file>`. Do not embed the whole message in a shell argument. No commit should occur at activation.
4. If staging, hook or commit fails: preserve the draft, inspect actual `HEAD` and status, keep human-message blocks intact, summarize material failure, and request authorization before potentially destructive retry. Never claim a failed action succeeded.
5. After apparent success, verify persisted `git show -s --format=%B HEAD` against the draft, allowing only Git's final newline, and check committed file paths. If mismatch, **retain the draft**; do not amend automatically. If fully verified, delete the temporary message file and close cycle. A later explicit invocation starts fresh without recycling earlier requests.
6. If recording is canceled or session ends before commit, do not transfer the draft into a new session. Delete on explicit cancellation authorization or agreed policy only; do not promise an automatic session-end hook that the environment does not provide.

## Acceptance and regression checks

- **Injected-Skill regression:** Given one human activation, a runtime-injected `<skill><name>...<path>...` document, and a human commit request, produce exactly **two** `User message` occurrences; one brief `Invoked skills` entry; **zero** embedded Skill content and **zero** absolute internal paths. Never treat a wrapper as human-authored without independent evidence.
- **Same-session cycles:** After first verified commit and draft deletion, start a fresh recording on a later explicit activation; second commit must not reinclude first-cycle human messages.
- **Literalness:** Preserve user whitespace, multiline content, repeated *distinct* messages, and quoted Skill-looking text genuinely written by a human, with only necessary sensitive redactions.
- **Recovery:** Verify failed staging/commit retains the draft, while success verifies persisted message before deletion. Never claim automatic recording across agent context loss.
- **Model and scope:** `Model` is self-reported or `Unknown`; never claim runtime verification, query model settings, or include reasoning effort. CLI versions are optional. Keep relevant timestamps and environment changes evidence-based; do not leak private locations.
