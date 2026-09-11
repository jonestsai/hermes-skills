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

1. **Playbook first.** Read the user's request and match it to a playbook file in the playbooks folder (bug-fix, perf-issue, investigation, feature, refactoring, prototype, hillclimb, shipping, babysit, autonomous-run, orchestrate, ... — full list in the Reference index below). Read that playbook file before planning. If nothing fits, default to investigation.md for read-only questions and feature.md for builds.
2. **Principles ground every decision.** Before writing code, load principle-laziness-protocol.md, principle-fix-root-causes.md, and principle-minimize-reader-load.md (all in the principles folder — full list below). Apply others as the task hits them (each principle file is short). Name each principle that shaped a decision, and the specific choice it changed.
3. **Subagents get fresh eyes.** Adversarial/review spawns use `delegate_task` with the brief from `references/agents/comment-sicko.md` or `references/agents/poteto-agent.md` passed VERBATIM as the child's context (children must not see the parent's defensiveness). Default child settings: leaf role, background spawn, parent model (this port is single-model — do not configure per-child models).
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
- TypeScript guidance lives in `references/workflow/typescript-refs/patterns.md` — applies to .ts work only
- Cursor-only slash commands do not exist; invocation is by phrase ("poteto-mode: ...")
- `/deslop` (from cursor-team-kit) is ported at `references/workflow/deslop.md` — run it over the diff before commits when a playbook calls for it. `deslop` = code slop; `unslop` = prose slop; different tools.
- `control-ui` (browser/Electron/web UI verification) → `browser_exec` or `drive_preview`; `control-cli` (CLI/TUI verification) → `terminal` with `pty=true` where interactive. These carry pstack's "prove it on the real surface" discipline — always verify on the actual artifact, never just unit tests.
- create-verification-skill output `.cursor/skills/verify-<app>/` → the Hermes skills tree: `~/.hermes/skills/verification/verify-<app>/` (default), or `~/.hermes/profiles/<p>/skills/verification/verify-<app>/` when one profile owns the app (e.g. walking-atlas). The generated frontmatter (name + description) registers it as a real Hermes skill, so agents can discover and trigger it; the feature map is plain files read via `read_file`. maintain-verification-skill's "usually `.cursor/skills/verify-*/`" resolves to the same location. Generated verify skills are manual (never hub/tap-managed).
- Cursor `environment: "cloud"` agents / cloud-sleeper wake chains → `delegate_task` children (local); wake cadences → `cronjob` scheduled ticks.
- `/loop` (Cursor's recurring self-wake) → `terminal(background=true)` with a sleep loop, or a `cronjob` when the cadence is 30min+; report each iteration per autonomous-run.md.
- pstack's watch-pr tool (PR merge-state watcher) → `gh pr view --json mergeable,state,statusCheckRollup` via `terminal`. (The original bun scripts were not ported.)
- pstack's orch runner (orchestration) → `delegate_task` batch spawns per orchestrate.md. (Not ported.)
- pstack's agent-store `docs/` → `~/.hermes/plans/` or the repo's own `docs/`.
- `~/.cursor/rules/pstack-models.mdc` model panels (arena/swarm/interrogate) → not ported; ignore model lists entirely, run all roles on the parent chat model.
- References keep their original frontmatter (harmless; these are read via `skill_view` file reads / `read_file`, not the skill loader). Playbooks mention Cursor-isms (`/loop`, `Task` calls, bugbot, model routing): translate them on the fly per this table, and ignore per-model defaults — this port runs the parent chat model everywhere.

## Reference index

Playbooks (each file listed here ships in the bundle): `references/playbooks/authoring-a-skill.md`, `references/playbooks/autonomous-run.md`, `references/playbooks/autopilot-full.md`, `references/playbooks/autopilot-stack.md`, `references/playbooks/babysit.md`, `references/playbooks/bug-fix.md`, `references/playbooks/eval.md`, `references/playbooks/feature.md`, `references/playbooks/hillclimb.md`, `references/playbooks/investigation.md`, `references/playbooks/multi-phase-plan.md`, `references/playbooks/opening-a-pr.md`, `references/playbooks/orchestrate.md`, `references/playbooks/pause-safely.md`, `references/playbooks/perf-issue.md`, `references/playbooks/prototype.md`, `references/playbooks/refactoring.md`, `references/playbooks/runtime-forensics.md`, `references/playbooks/session-pickup.md`, `references/playbooks/shipping.md`, `references/playbooks/trace-forensics.md`, `references/playbooks/visual-parity.md`, `references/playbooks/worktree-cleanup.md`.

Principles (each file listed here ships in the bundle): `references/principles/principle-attack-the-premise.md`, `references/principles/principle-boundary-discipline.md`, `references/principles/principle-build-the-lever.md`, `references/principles/principle-encode-lessons-in-structure.md`, `references/principles/principle-exhaust-the-design-space.md`, `references/principles/principle-experience-first.md`, `references/principles/principle-fix-root-causes.md`, `references/principles/principle-foundational-thinking.md`, `references/principles/principle-guard-the-context-window.md`, `references/principles/principle-laziness-protocol.md`, `references/principles/principle-make-operations-idempotent.md`, `references/principles/principle-migrate-callers-then-delete-legacy-apis.md`, `references/principles/principle-minimize-reader-load.md`, `references/principles/principle-model-the-domain.md`, `references/principles/principle-never-block-on-the-human.md`, `references/principles/principle-outcome-oriented-execution.md`, `references/principles/principle-prove-it-works.md`, `references/principles/principle-redesign-from-first-principles.md`, `references/principles/principle-separate-before-serializing-shared-state.md`, `references/principles/principle-sequence-verifiable-units.md`, `references/principles/principle-subtract-before-you-add.md`, `references/principles/principle-test-behavior-not-implementation.md`, `references/principles/principle-type-system-discipline.md`.

Workflow skills (each file listed here ships in the bundle): `references/workflow/architect.md`, `references/workflow/arena.md`, `references/workflow/blast-radius.md`, `references/workflow/create-verification-skill.md`, `references/workflow/deslop.md`, `references/workflow/figure-it-out.md`, `references/workflow/how.md`, `references/workflow/interrogate.md`, `references/workflow/maintain-verification-skill.md`, `references/workflow/recall.md`, `references/workflow/reflect.md`, `references/workflow/show-me-your-work.md`, `references/workflow/swarm.md`, `references/workflow/tdd.md`, `references/workflow/teach.md`, `references/workflow/technical-writing.md`, `references/workflow/unslop.md`, `references/workflow/why.md` — plus their supporting files: `references/workflow/how-refs/explainer-prompt.md`, `references/workflow/how-refs/explorer-prompt.md`, `references/workflow/interrogate-refs/code-quality-review.md`, `references/workflow/interrogate-refs/lead-judgment.md`, `references/workflow/interrogate-refs/reviewer-prompt.md`, `references/workflow/interrogate-refs/rubric.md`, `references/workflow/reflect-refs/divergent-reviewer.md`, `references/workflow/reflect-refs/judgment-reviewer.md`, `references/workflow/reflect-refs/synthesizer.md`, `references/workflow/reflect-refs/tooling-reviewer.md`, `references/workflow/show-me-your-work-refs/decision-log-template.tsv`, `references/workflow/show-me-your-work-scripts/log.sh`, `references/workflow/typescript-refs/patterns.md`, `references/workflow/why-refs/epistemics.md`, `references/workflow/why-refs/investigator-prompt.md`, `references/workflow/why-refs/source-playbook.md`, `references/workflow/why-refs/sources/code-archaeology.md`, `references/workflow/why-refs/sources/databricks.md`, `references/workflow/why-refs/sources/datadog.md`, `references/workflow/why-refs/sources/incident-postmortem.md`, `references/workflow/why-refs/sources/linear.md`, `references/workflow/why-refs/sources/notion.md`, `references/workflow/why-refs/sources/sentry.md`, `references/workflow/why-refs/sources/slack.md`, `references/workflow/create-verification-refs/feature-map-example/README.md`, `references/workflow/create-verification-refs/feature-map-example/create-note.md`, `references/workflow/create-verification-refs/feature-map-example/search.md`.

Agent briefs (each file listed here ships in the bundle): `references/agents/comment-sicko.md`, `references/agents/poteto-agent.md`.

Standalone: `references/no-comments.md`, `references/bugbot-triage.md`.

(Install note: the skills hub bundles exactly the files listed above — this index doubles as the bundle manifest, so keep it in sync when files are added or removed.)

## Verification

- Skill loads and triggers only on explicit "poteto-mode" mention
- Playbooks reference real files (each playbook named in the Reference index exists on disk)
- A trial run ("poteto-mode: investigation — how does X work") reads investigation.md, applies principles, returns cited answer

## Source

https://github.com/cursor/plugins/tree/main/pstack (MIT, Lauren Tan). Copied 2026-08-29 from main.
