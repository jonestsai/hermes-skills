---
name: poteto-mode
description: Rigorous engineering workflows, ported from poteto's pstack. Use ONLY when the user explicitly says 'poteto-mode'.
version: 0.1.0
author: Lauren Tan (poteto), Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [engineering, workflow, review, discipline]
    related_skills: [systematic-debugging, requesting-code-review, test-driven-development]
---

# Poteto Mode (pstack port)

Rigorous, verifiable engineering discipline: reproduce before fixing, root causes over symptom patches, minimal diffs, adversarial review, evidence with every claim. Ported from poteto's pstack (Cursor) — single-model, Hermes tools.

**Activates ONLY on explicit user trigger** ("poteto-mode", "use poteto mode", or naming a specific playbook/reference below). Never auto-invoke; the user pays the token cost on purpose.

## Operating rules

1. **Playbook first.** Read the user's request and match it to a playbook in `references/playbooks/` (bug-fix, perf-issue, investigation, feature, refactoring, prototype, hillclimb, shipping, babysit, autonomous-run, orchestrate, ...). Read that playbook file before planning. If nothing fits, default to `investigation.md` for read-only questions and `feature.md` for builds.
2. **Principles ground every decision.** Before writing code, load `references/principles/principle-laziness-protocol.md`, `principle-fix-root-causes.md`, and `principle-minimize-reader-load.md`. Apply others as the task hits them (each `principle-*` file is short). Name each principle that shaped a decision, and the specific choice it changed.
3. **Subagents get fresh eyes.** Adversarial/review spawns use `delegate_task` with the brief from `references/agents/` passed VERBATIM as the child's context (children must not see the parent's defensiveness). `references/agents/comment-sicko.md` is the comment review brief. Default child settings: leaf role, background spawn, parent model (this port is single-model — do not configure per-child models).
4. **No comments pass before review/shipping.** Read `references/no-comments.md`; spawn the comment-sicko child via `delegate_task` with its brief; triage its report (keep-list exceptions survive only with proof; encode proven constraints as type/test/lint, then delete the comment; root-cause what comments papered over).
5. **Verify, don't claim.** Every playbook ends with evidence: commands run, output observed, tests green. If a step can't be verified, say so — never fabricate results. `references/workflow/show-me-your-work.md` for long/autonomous runs (decision trail).
6. **Unslop all prose.** Final replies follow `references/workflow/unslop.md` (cut AI tells, no filler, no "great question"). Technical docs follow `references/workflow/technical-writing.md`.
7. **Comment Sicko brief lives at `references/agents/comment-sicko.md`** — when spawning it, read that file and pass its content verbatim as the child's context.

## Adaptation notes (Cursor → Hermes)

- `Task`/`subagent_type` → `delegate_task` (goal/context/role; leaf children)
- `AskQuestion` → `clarify`
- bugbot / cursor-team-kit reviews → `requesting-code-review` + `github-code-review` skills; Bugbot thread handling per `references/bugbot-triage.md`
- graphite stacks → plain git branches/PRs via the `github-pr-workflow` skill
- `/setup-pstack` model routing → not ported (single model by design)
- TypeScript guidance lives in `references/workflow/typescript-refs/` — applies to .ts work only
- Cursor-only slash commands do not exist; invocation is by phrase ("poteto-mode: ...")
- `/deslop` (from cursor-team-kit) is ported at `references/workflow/deslop.md` — run it over the diff before commits when a playbook calls for it. `deslop` = code slop; `unslop` = prose slop; different tools.
- `control-ui` (browser/Electron/web UI verification) → `browser_exec` or `drive_preview`; `control-cli` (CLI/TUI verification) → `terminal` with `pty=true` where interactive. These carry pstack's "prove it on the real surface" discipline — always verify on the actual artifact, never just unit tests.
- Cursor `environment: "cloud"` agents / cloud-sleeper wake chains → `delegate_task` children (local); wake cadences → `cronjob` scheduled ticks.
- `/loop` (Cursor's recurring self-wake) → `terminal(background=true)` with a sleep loop, or a `cronjob` when the cadence is 30min+; report each iteration per autonomous-run.md.
- `scripts/watch-pr` (PR merge-state watcher) → `gh pr view --json mergeable,state,statusCheckRollup` via `terminal`.
- `scripts/orch` (orchestration runner) → `delegate_task` batch spawns per orchestrate.md.
- pstack's agent-store `docs/` → `~/.hermes/plans/` or the repo's own `docs/`.
- `~/.cursor/rules/pstack-models.mdc` model panels (arena/swarm/interrogate) → not ported; ignore model lists entirely, run all roles on the parent chat model.
- References keep their original frontmatter (harmless; these are read via `skill_view` file reads / `read_file`, not the skill loader). Playbooks mention Cursor-isms (`/loop`, `Task` calls, bugbot, model routing): translate them on the fly per this table, and ignore per-model defaults — this port runs the parent chat model everywhere.

## Reference index

Playbooks (`references/playbooks/`): authoring-a-skill.md, autonomous-run.md, autopilot-full.md, autopilot-stack.md, babysit.md, bug-fix.md, eval.md, feature.md, hillclimb.md, investigation.md, multi-phase-plan.md, opening-a-pr.md, orchestrate.md, pause-safely.md, perf-issue.md, prototype.md, refactoring.md, runtime-forensics.md, session-pickup.md, shipping.md, trace-forensics.md, visual-parity.md, worktree-cleanup.md.

Principles (`references/principles/`): principle-boundary-discipline.md, principle-build-the-lever.md, principle-encode-lessons-in-structure.md, principle-exhaust-the-design-space.md, principle-experience-first.md, principle-fix-root-causes.md, principle-foundational-thinking.md, principle-guard-the-context-window.md, principle-laziness-protocol.md, principle-make-operations-idempotent.md, principle-migrate-callers-then-delete-legacy-apis.md, principle-minimize-reader-load.md, principle-model-the-domain.md, principle-never-block-on-the-human.md, principle-outcome-oriented-execution.md, principle-prove-it-works.md, principle-redesign-from-first-principles.md, principle-separate-before-serializing-shared-state.md, principle-sequence-verifiable-units.md, principle-subtract-before-you-add.md, principle-type-system-discipline.md.

Workflow (`references/workflow/`): architect.md, arena.md, blast-radius.md, create-verification-skill.md, deslop.md, figure-it-out.md, how.md, interrogate.md, maintain-verification-skill.md, recall.md, reflect.md, show-me-your-work.md, swarm.md, tdd.md, teach.md, technical-writing.md, unslop.md, why.md — plus their supporting files: `references/workflow/how-refs/critic-prompt.md`, `references/workflow/how-refs/critique-rubric.md`, `references/workflow/how-refs/explainer-prompt.md`, `references/workflow/how-refs/explorer-prompt.md`, `references/workflow/interrogate-refs/code-quality-review.md`, `references/workflow/interrogate-refs/lead-judgment.md`, `references/workflow/interrogate-refs/reviewer-prompt.md`, `references/workflow/interrogate-refs/rubric.md`, `references/workflow/reflect-refs/divergent-reviewer.md`, `references/workflow/reflect-refs/judgment-reviewer.md`, `references/workflow/reflect-refs/synthesizer.md`, `references/workflow/reflect-refs/tooling-reviewer.md`, `references/workflow/show-me-your-work-refs/decision-log-template.tsv`, `references/workflow/show-me-your-work-scripts/log.sh`, `references/workflow/typescript-refs/patterns.md`, `references/workflow/why-refs/epistemics.md`, `references/workflow/why-refs/investigator-prompt.md`, `references/workflow/why-refs/source-playbook.md`, `references/workflow/why-refs/sources/code-archaeology.md`, `references/workflow/why-refs/sources/databricks.md`, `references/workflow/why-refs/sources/datadog.md`, `references/workflow/why-refs/sources/incident-postmortem.md`, `references/workflow/why-refs/sources/linear.md`, `references/workflow/why-refs/sources/notion.md`, `references/workflow/why-refs/sources/sentry.md`, `references/workflow/why-refs/sources/slack.md`, `references/workflow/why-refs/sources/synthesizer-prompt.md`, `references/workflow/create-verification-refs/feature-map-example/README.md`, `references/workflow/create-verification-refs/feature-map-example/create-note.md`, `references/workflow/create-verification-refs/feature-map-example/search.md`.

Agents (`references/agents/`): comment-sicko.md, poteto-agent.md.

Standalone: `references/no-comments.md`, `references/bugbot-triage.md`.

(Install note: the skills hub bundles exactly the files listed above — this index doubles as the bundle manifest, so keep it in sync when files are added or removed.)

## Verification

- Skill loads and triggers only on explicit "poteto-mode" mention
- Playbooks reference real files (`references/playbooks/<name>.md` all exist)
- A trial run ("poteto-mode: investigation — how does X work") reads investigation.md, applies principles, returns cited answer

## Source

https://github.com/cursor/plugins/tree/main/pstack (MIT, Lauren Tan). Copied 2026-08-29 from main.
