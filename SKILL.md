---
name: pm-workflow
description: >-
  Mandatory AI product manager workflow for product/design/PRD work with project-local decision memory. Use whenever the user discusses product requirements, feature scope, UX/UI design decisions, interaction flows, product tradeoffs, design review conclusions, /pm, /note, /digest, 记录产品讨论, 记录设计决策, 校准 observations, 整理产品决策记录, 生成 PRD, 写需求文档, or asks to preserve product context. After any product/design discussion turn, proactively ask whether to record decisions. After every 3 recorded notes since the last calibration, proactively ask whether to calibrate observations. Before drafting any PRD, first ask whether to record the current round, then ask whether to calibrate observations, then draft. Digest/calibration must never silently rewrite observations: show grouped findings first and wait for explicit user confirmation.
---

# PM Workflow

Use this skill to run a reusable AI 产品经理工作流 across projects while keeping each project's memory isolated in that project's `.claude/observations.md`.

## Trigger Contract

This skill is not a background hook. It only runs when loaded for the current turn. Because the frontmatter `description` is the only part visible before loading, keep all proactive trigger conditions in that description.

When this skill is loaded after a product/design discussion, treat the proactive prompts as part of the current turn's work:

- Ask whether to record the current round's product/design decisions unless the user already asked to record them or explicitly said not to.
- After a successful Note workflow, check the number of notes since the last Digest calibration and ask whether to calibrate when the count reaches a multiple of 3.
- Before any PRD workflow, always run the PRD preflight unless the user explicitly asks to skip recording and calibration.
- Never run Digest silently or rewrite observations before the user confirms the grouped findings.

## Project Memory

Before running any workflow, identify the project root:

1. Prefer the nearest ancestor containing an existing `.claude/observations.md`.
2. Otherwise prefer the nearest ancestor containing `.git`.
3. Otherwise use the current working directory.

Use `<project-root>/.claude/observations.md` as the only memory file for the current project. Do not use a global `observations.md`, and do not write another project's memory file.

If the project memory file is missing and the requested workflow needs it, create `<project-root>/.claude/observations.md` from `references/observations-template.md` before continuing.

## Workflow Router

Choose the workflow based on the user request and current turn context. Most requests use exactly one workflow, except PRD requests require the preflight sequence below.

- **Note workflow**: Use when the user asks to record this round, 记录这轮产品讨论, 记录设计决策, 记一下, preserve decisions after a product/design discussion, or agrees to the proactive record prompt. Read `references/note.md`, then append a dated entry to the project memory file.
- **Digest workflow**: Use when the user asks to 整理产品决策记录, 校准 observations, digest accumulated notes, remove conflicts, clean up project memory, or agrees to the proactive calibration prompt. Read `references/digest.md`, present grouped findings first, ask for user confirmation on keep/delete/modify decisions, then rewrite the project memory only after confirmation.
- **PRD workflow**: Use when the user asks to 生成 PRD, 写需求文档, 起草产品需求文档, or turn accumulated decisions into a product spec. Run PRD Preflight first, read `references/pm.md`, then draft the PRD from the project memory and current conversation context.

## Proactive Prompts

- After each product/design discussion turn, proactively ask the user whether to record the decisions from this round. If the user agrees, run the Note workflow.
- Track how many Note workflow entries have been recorded in the current project since the last Digest workflow calibration. After every third recorded note, proactively ask the user whether to calibrate the accumulated observations. If the user agrees, run the Digest workflow.
- If the count is unknown, infer it from `.claude/observations.md`: count note entries after the latest "最近校准日期" or other explicit digest/calibration marker. If no calibration marker exists, count all dated note entries.
- Do not run Digest workflow silently. It must present grouped findings and get user confirmation before rewriting `.claude/observations.md`.

## PRD Preflight

Before starting any PRD workflow:

1. Ask whether the user wants to record the current round's decisions first.
2. If the user agrees, run the Note workflow and append the note.
3. Ask whether the user wants to calibrate observations before drafting the PRD.
4. If the user agrees, run the Digest workflow and complete the user-confirmed rewrite.
5. Only then run the PRD workflow using the latest project memory and current conversation context.

If the user declines either preflight step, continue to the next step without blocking. If the user explicitly asks to skip recording or calibration, respect that instruction and proceed directly to the PRD workflow.

## Operating Rules

- Keep observations concrete enough for a future session to reconstruct product intent without the original conversation.
- Mark changes to earlier decisions explicitly with the reason for the change.
- Keep user preference notes only when there is clear behavioral evidence.
- For PRDs, distinguish confirmed observations from inference or missing information using the marking rules in `references/pm.md`.
- For PRDs, write for product stakeholders who may not have code access. Do not include code snippets, implementation code, or frontend/backend file-level instructions. Only include concrete input/output examples when a step involves an API call or model call; when an API/model call is involved, also document the required model/calling configuration if known. Put API/model call details inside the corresponding detailed requirement section, not in a standalone API/model chapter.
- Preserve UTF-8 Chinese content.
- Do not modify unrelated project files.

## References

- `references/note.md`: detailed note-taking procedure, append format, and post-note calibration prompt.
- `references/digest.md`: detailed calibration procedure, grouped finding review format, and no-silent-rewrite guardrail.
- `references/pm.md`: required PRD preflight, PRD generation structure, and source-marking rules.
- `references/observations-template.md`: template for new project-local memory files.
