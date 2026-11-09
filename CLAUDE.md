# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A Markdown-only Skill for UGREEN NAS / Uliya, entered in the UGREEN NAS Skill Hackathon. There is no code, build or test runner: the deliverable is `SKILL.md`, `README.md` and `references/`, which an agent on the NAS follows. Keep this folder outside `~/.claude/skills/`, so Claude Code does not load it as one of its own skills.

`_private/` holds the build guide (`zBuild-ProofPack.txt`: stages, deadlines, submission steps), the hackathon rules (`starter-kit.txt`) and the proposal. Read those two `.txt` files before packaging or submitting work. Content from `_private/` stays out of the shipped files.

## Commands

Validate with the UGOS Skill Creation Tool (`uliya-skill-kit`, installed in `~/.claude/skills/`). Repeat until it reports no required changes:

```
Help me check this skill.
```

Package from the home folder, then confirm the listing shows only `SKILL.md`, `README.md` and `references/`:

```bash
cd ~
zip -rX proof-pack.zip proof-pack \
  -x "proof-pack/_private/*" "proof-pack/.git/*" "proof-pack/.gitignore" \
     "proof-pack/CLAUDE.md" "*.DS_Store"
unzip -l proof-pack.zip
```

If the kit requires `SKILL.md` at the ZIP root: `cd ~/proof-pack && zip -rX ../proof-pack.zip SKILL.md README.md references`.

Limits: ZIP under 10 MB compressed and uncompressed, each file under 1 MB, fewer than 100 files, Markdown only.

## How the files fit together

`SKILL.md` is the ordered workflow (Steps 1 to 10). Each `references/` file is disclosed detail that a step points to:

- `claim-recipes.md`: per-claim checklists, keywords, fields, "weak if" rules and reconcile checks, consumed by Steps 1 to 6
- `evidence-rules.md`: status decisions, source independence, time tolerances, verdict-free wording, versions
- `pack-format.md`: exact output formats
- `failure-handling.md`: every failure case and its exact user message
- `test-plan.md`: expected results for the finalist NAS run
- `disclosures.md`: hackathon disclosures, including AI tools used

Meanings are repeated across these files, so an edit is complete only when every copy agrees:

- **Step numbers** are cited in the `SKILL.md` capability table and in `failure-handling.md`, `pack-format.md` and `test-plan.md`.
- **The five statuses** (Verified, Single source, Conflict, Weak, Missing) and their display order appear in `SKILL.md`, `evidence-rules.md`, `pack-format.md`, `test-plan.md` and `README.md`.
- **The main failure rules** in `SKILL.md` summarize `failure-handling.md`.
- **Data handling** claims appear in `SKILL.md`, `README.md` and `disclosures.md`.
- **The frontmatter `description`** must match the actual workflow; the kit check flags drift.
- **`test-plan.md` expected results** must follow any change to recipes or evidence rules.

## Rules for editing the Skill

These come from the hackathon rules and the Skill's core promises.

- Every step maps to a capability in the UGOS Skill Capability Scope, called through its expert agent. When support is uncertain, ask the kit; its documentation outranks `starter-kit.txt`.
- Each step consumes the actual output of the previous one: event IDs, photo IDs, file paths.
- Writes happen only in Step 9, after the explicit yes in Step 8. Originals are only ever read and copied.
- When nothing is found, say so and stop or mark the item Missing, naming what was searched.
- The Skill reports facts and leaves disputes to the user: conflicts appear as two sourced facts, with no verdict words.
- Person results stay in the session and out of the pack and note.
- Finished files contain only complete Markdown: no scripts, external downloads, credentials, TODOs or placeholders.
- `README.md` stays in plain language for non-technical NAS owners.
- `disclosures.md` must reflect the AI tools actually used.
