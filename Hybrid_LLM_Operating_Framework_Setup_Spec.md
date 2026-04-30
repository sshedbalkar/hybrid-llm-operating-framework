# Hybrid LLM Operating Framework Setup Spec

## Purpose

Set up a reusable, low-token, validation-first operating framework in any repository so future LLM sessions can cold-start, continue work, and update the project without broad rediscovery or transcript dependence.

This spec must work in two cases:

- repositories that already contain meaningful source truth
- day-zero repositories that are empty, nearly empty, or only contain a name, manifest, or starter skeleton

## How To Use This Spec

Use [README.md](README.md) for setup instructions and the deployment prompt.

This file is the framework contract. A deploying LLM must read it before writing files, then discover all `[PLACEHOLDER]` values live from the target repository.

## Core Invariants

- Truth order: `source_of_truth > validated_shared_memory > local_private_scratch > chat_history`
- Route before read.
- Validate every task before marking outcomes verified.
- Shared memory stores routing and reusable facts, not source content.
- Compaction is mandatory. Token health is a first-class requirement.
- If shared memory conflicts with source truth, source truth wins immediately.
- If a cache lookup misses, fall back to source truth; absence from cache is not proof of absence.
- In day-zero repositories, `project-foundation` is valid source truth until richer source truth emerges.
- Planned structures may exist in shared memory, but must be labeled `planned` or `active`; only source-backed facts may be `verified`.
- Shared cache files must declare lookup strategy before entries.

## Lifecycle Model

Use this lifecycle for framework behavior:

- `day_zero`: no reliable source truth beyond a name, empty folders, minimal manifests, or starter boilerplate
- `early_build`: initial source truth exists and some real code/docs/config/CI artifacts exist
- `active_growth`: repeated feature work is happening and real modules, boundaries, validators, or deployable units are accumulating
- `mature_repo`: topology, boundaries, validators, and workflows are stable enough to enforce full routing depth and compaction budgets

Promotion rules:

- `planned -> active`: a workstream, directory, manifest entry, schema, pipeline, or docs surface now exists in repo truth
- `active -> verified`: source truth exists and `validate_full` proves the current description
- `verified -> retired`: the unit or workstream is intentionally removed, superseded, or archived

## Required Live Discovery

The LLM must discover these values before setup:

- `[PROJECT_STATE]`: `day_zero | early_build | active_growth | mature_repo`
- `[BOOTSTRAP_FILE]`: usually `AGENTS.md`, `README_AGENT.md`, or equivalent root guidance file
- `[PROJECT_FOUNDATION_FILE]`: existing root `README`, existing docs index/ADR/architecture file, or new `docs/project-foundation.md`
- `[SOURCE_OF_TRUTH_PATHS]`: canonical docs, code roots, schemas, manifests, build files, deployment config, runbooks
- `[SHARED_CONTEXT_DIR]`: default `context/`
- `[CARD_INDEX_FILE]`: default `[SHARED_CONTEXT_DIR]/doc-cards.md` for documentation-heavy repos; otherwise `[SHARED_CONTEXT_DIR]/cards.md`
- `[ROUTING_MAP_FILE]`: default `[SHARED_CONTEXT_DIR]/routing-map.md`; use a domain-specific name such as `[SHARED_CONTEXT_DIR]/bstd-routing-map.md` when that is already established
- `[VALIDATOR_SPECS_DIR]`: default `[SHARED_CONTEXT_DIR]/validators/` when validators are route-bound specs
- `[ENFORCEMENT_TOOL_DIR]`: optional reusable validator/enforcer package path such as `tools/bstd-enforcer/`
- `[LOCAL_SCRATCH_LOCATIONS]`: gitignored agent-local files and directories
- `[WORK_UNIT_KIND]`: doc, service, package, app, library, infra module, schema set, pipeline, subsystem, or workstream
- `[VALIDATION_COMMANDS]`: parse, lint, typecheck, unit, integration, build, migration, runtime smoke, perf
- `[PROJECT_SIZE]`: small, medium, or large/monorepo
- `[ROUTING_DEPTH]`: flat, area-based, or product->area->unit
- `[EXISTING_GUIDANCE_FILES]`: any existing instructions, contributor docs, architecture docs, or repo maps
- `[GITIGNORE_PATTERNS]`: local scratch locations that must remain untracked
- `[PLANNED_WORKSTREAMS]`: initial major workstreams, if the repo is day-zero or early-build

## Source Of Truth Bootstrap

When the repository is `day_zero`, establish one minimal canonical project-intent artifact before building shared context.

Choose `[PROJECT_FOUNDATION_FILE]` in this order:

1. existing root `README` if it already states purpose, scope, and initial direction clearly
2. existing docs index, ADR, architecture overview, or equivalent if it is more canonical than the README
3. new `docs/project-foundation.md` if nothing adequate exists

This file becomes the initial source truth for:

- project goal
- intended users
- target platforms
- initial stack intent
- constraints
- non-goals
- planned workstreams
- initial validation plan
- decision owners

Do not create deeper shared context until this source truth exists.

## Project Size Classification

Use this deterministic classification:

- `small`: <=30 real leaf work units and a single flat routing file can stay <=300 lines
- `medium`: >30 real leaf work units, but top-level areas can each route to <=12 likely targets cheaply
- `large`: multiple products, monorepo slices, or any area that cannot cheaply narrow to <=12 likely targets

## Target File Topology

Use this topology unless the repo already has a clearly better equivalent:

- `[BOOTSTRAP_FILE]`
- `[PROJECT_FOUNDATION_FILE]`
- `[SHARED_CONTEXT_DIR]/README.md`
- `[SHARED_CONTEXT_DIR]/llm-operating-guide.md`
- `[SHARED_CONTEXT_DIR]/repo-map.md`
- `[CARD_INDEX_FILE]`
- `[ROUTING_MAP_FILE]`
- `[VALIDATOR_SPECS_DIR]/README.md` if route-bound validators exist or are being introduced
- `[SHARED_CONTEXT_DIR]/decision-log.md`
- `[SHARED_CONTEXT_DIR]/open-work.md`

Routing depth by size and maturity:

- `day_zero` or `small`: one flat card index, usually `[CARD_INDEX_FILE]`
- `medium`: `[SHARED_CONTEXT_DIR]/areas/<area>.md`
- `large`: `[SHARED_CONTEXT_DIR]/products/<product>/index.md`, `[SHARED_CONTEXT_DIR]/areas/<area>.md`, `[SHARED_CONTEXT_DIR]/units/<unit>.md`

Optional enforcement kit:

- `[ENFORCEMENT_TOOL_DIR]/`: CLI, schema, static registry, prompts, examples, tests, and config templates
- Create only when validation is meant to be packaged for reuse by other repos or CI
- Keep it pointer-based: no copied source-rule text

Local-only scratch:

- `CLAUDE.md`, `CODEX.md`, `.claude/`, `.codex/`, `prompts/`, or equivalents
- Always gitignored
- Always disposable
- Never source of truth

## Lifecycle Growth Rules

### day_zero

- Create only:
  - `[BOOTSTRAP_FILE]`
  - `[PROJECT_FOUNDATION_FILE]`
  - `repo-map`
  - flat card index
  - validator specs or bootstrap validator matrix
  - `decision-log`
  - `open-work`
  - top-level workstream cards
- Do not create deep routing.
- Do not create fabricated leaf unit cards.
- Use bootstrap validators if real validators do not exist yet.

### early_build

- Add cards only for components that now exist in manifests, code, docs, schemas, or CI config.
- Keep top-level workstream cards if they still help route future work.
- Promote planned entries to active when repo truth now supports them.
- Keep routing shallow unless thresholds trip.

### active_growth

- Split routing depth when thresholds trip.
- Add unit cards on first touch or on boundary creation.
- Prefer real deployable boundaries, public interfaces, schema roots, and docs roots over speculative internal subdivisions.
- Replace bootstrap validators with actual project validators when available.

### mature_repo

- Enforce full routing depth chosen by repo size.
- Enforce validator coverage for all major boundaries.
- Prune stale or low-value shared context aggressively.
- Keep cards, validators, and routing files within token budgets.

## Setup Path: Day-Zero Project

1. Detect that the repository is truly `day_zero`.
2. Choose or create `[PROJECT_FOUNDATION_FILE]` using the source-of-truth bootstrap rules.
3. Choose `[BOOTSTRAP_FILE]`. If absent, create it.
4. Create shared context files in `[SHARED_CONTEXT_DIR]`.
5. Create only top-level workstream cards in `[CARD_INDEX_FILE]`.
6. Use `kind: workstream` and `state: planned` or `active` for those cards.
7. Create validator specs or a bootstrap validator matrix if real validators do not exist yet.
8. Mark all shared-memory facts `unverified` until validators pass.
9. Run bootstrap validators plus any real validators that already exist.
10. Compact all setup files before completion.

## Setup Path: Existing Project

1. Inventory existing guidance and memory-like files first.
2. Preserve existing source-of-truth docs exactly unless setup requires references to them.
3. Choose `[PROJECT_FOUNDATION_FILE]` from existing canonical docs if possible; do not create a redundant foundation file if one already exists.
4. Merge existing useful guidance into `[BOOTSTRAP_FILE]` and shared context; do not duplicate rules.
5. If the repo already has a routing layer, normalize it instead of creating a parallel one.
6. Normalize existing shared cache files in place before building, backfilling, or updating entries: add missing `## Lookup Strategy`, preserve declared repo-native ordering, and refresh jump indexes.
7. Remove or quarantine duplicated, transcript-like, stale, or tool-specific content from tracked memory files.
8. Keep existing local private files local and untracked.
9. Create missing shared context files only where absent.
10. Backfill routing and unit cards using real repo boundaries only.
11. Validate before marking migrated memory as `verified`.
12. If any migrated fact cannot be source-linked or proven, keep it `unverified` or delete it.

## Dynamic Routing Rules

- Start reads at `[BOOTSTRAP_FILE]`, `repo-map`, `[ROUTING_MAP_FILE]`, and validator specs when validation is involved.
- Read `## Lookup Strategy` before scanning cache entries.
- Drill deeper only until target, direct dependencies, and proof path are unambiguous.
- Stop drilling once the candidate set is `<=12` likely targets and the current node remains compact.
- Default to flat routing in `day_zero` and most `early_build` repositories.
- Split a routing node if any of these are true:
  - `>30` children
  - `>300` lines
  - target size `>4k` tokens, hard cap `8k`
  - repeated co-reading still does not narrow quickly
- Merge sibling routing nodes upward only if they are nearly always co-read and remain under the node cap together.
- Never load sibling areas "for context" unless the task explicitly crosses their boundary.
- Never pre-generate deep routing for hypothetical future modules.

## Cache Miss Handling

A cache miss occurs when lookup strategy, jump index, route map, card index, validator index, or routing nodes do not identify the target clearly.

On cache miss:

1. Re-check the current file's `## Lookup Strategy`, jump index, aliases, and trigger terms.
2. Widen only one routing layer or cache group at a time until there are `<=12` plausible targets.
3. Search source truth directly using stable identifiers, paths, manifests, schemas, docs, or build config.
4. If source truth identifies a reusable target, backfill or correct the owning cache entry in the same task.
5. Keep new or corrected cache facts `unverified` until source-linked validation passes.
6. If source truth still cannot identify the target, record the miss as open work or ask only if the ambiguity blocks the task.
7. Never treat cache absence as evidence that a source file, route, validator, or workstream does not exist.

## Context Growth Policy

- Start tiny.
- Add shared context only when a workstream, boundary, validator, or decision becomes reusable.
- Add unit cards only when a real unit exists or a boundary is approved.
- Add route maps only when route selection becomes reusable across tasks.
- Add validator specs only when validation facets, scoring, or static/LLM split decisions become reusable.
- Add enforcement tooling only when the validator behavior must run outside one chat session.
- Replace provisional descriptions with source-backed descriptions as the repo matures.
- When shared cache entries change, update ordering and jump indexes in the same edit.
- Delete low-value, stale, duplicated, or single-use memory aggressively.
- If a routing split is not yet justified, do not create one.

## Cached Data Classes

Tracked shared context may cache only these durable data types:

- repo topology: source roots, scripts, inventory counts, edit workflow, proof commands
- card index: one compact card per document, unit, app, service, module, or workstream
- route map: route IDs, trigger terms, source docs, overlays, validation facets
- validator specs: route binding, applicability, scoring, static coverage, LLM gap coverage, evolution rules
- enforcement kit metadata: CLI contract, config schema, static registry, prompts, examples, tests
- decisions: durable structural choices with source references
- open work: active gaps with expected end state and removal condition

Do not cache source-rule bodies, long excerpts, transcripts, terminal dumps, generated diffs, or copied reports.

## Cache Lookup Organization

Every repo map, card index, route map, and validator index must declare `## Lookup Strategy` before entries. Routing nodes must declare it once they exceed `100` lines or `12` entries.

Default ordering:

- route maps: sort by stable `route.<id>`
- card indexes: group by `kind`, then sort by stable `lookup_key`
- validator indexes: sort by bound route ID
- repo maps: keep fixed framework sections first, then sort lists alphabetically inside each section
- large routing nodes: group by semantic area, then sort child IDs alphabetically inside each group

Lookup rules:

- Prefer stable IDs over display names because names drift.
- Use lowercase kebab-case IDs or dot-delimited route IDs.
- Add a top-of-file jump index when a cache file has `>12` entries or `>100` lines.
- Keep jump indexes pointer-only; do not duplicate source-rule content.
- If repo-native ordering already exists, declare it and preserve it unless it blocks fast lookup.

Update rules:

- Building, backfilling, and updating shared cache entries all use the same lookup strategy.
- Before editing a shared cache file, read its `## Lookup Strategy`.
- Insert, rename, move, or delete entries according to the declared ordering.
- Refresh the jump index whenever entries change or the file crosses a jump-index threshold.
- If the existing strategy is missing, add it before changing entries.
- If the existing strategy conflicts with repo-native lookup, update the strategy first, then reorder entries.

## Exact Schemas

### Project Foundation Schema

```md
# [PROJECT] Foundation

goal:
users:
platforms:
initial_stack:
constraints:
non_goals:
planned_workstreams:
initial_validation_plan:
decision_owners:
```

### Bootstrap File Schema

```md
# [PROJECT] Agent Guide

project_state:
project_foundation_file:

## Core Rules
- truth order
- routing rule
- validation rule
- compaction rule
- conflict-resolution rule
- source-of-truth locations
- local scratch rule
- closeout rule

## Read Order
1. [BOOTSTRAP_FILE]
2. [PROJECT_FOUNDATION_FILE]
3. [SHARED_CONTEXT_DIR]/repo-map.md
4. [ROUTING_MAP_FILE]
5. relevant cards or routing nodes only
6. relevant validator specs only when validation is involved
7. target sources only

## Cache Update Rule
- read lookup strategy before editing shared cache files
- preserve declared ordering and refresh jump indexes when entries change
```

### Routing Node Schema

```md
# [NODE_ID]

## Lookup Strategy
- primary_lookup: child_id
- ordering: semantic_group_first, alphabetical_within_group
- jump_index: required when node has >12 entries or >100 lines
- entry_id_format: stable lowercase kebab-case

scope:
children:
open_if:
depends_on:
state:
promotion_trigger:
validate_fast:
validate_full:
```

### Repo Map Schema

```md
# Repo Map

## Lookup Strategy
- primary_lookup: section + stable_path_or_unit_id
- ordering: fixed_framework_sections_first, alphabetical_within_section
- jump_index: required when file has >12 entries or >100 lines
- entry_id_format: repo-native path or stable lowercase kebab-case

## Jump Index
- [section]:

## Source Truth

## Shared Context

## Validation
```

### Card Index Schema

```md
# [PROJECT] Cards

## Lookup Strategy
- primary_lookup: card_kind + lookup_key
- ordering: semantic_group_first, alphabetical_within_group
- jump_index: required when file has >12 entries or >100 lines
- entry_id_format: stable lowercase kebab-case

## Jump Index
- kind.[kind]:

## Cards

### [lookup_key]
- path:
- kind:
- purpose:
- state:
```

### Route Map Schema

```md
# [DOMAIN] Routing Map

## Purpose
- source truth location
- card index location
- route selection rule
- validator lookup rule

## Lookup Strategy
- primary_lookup: route_id
- ordering: route_id
- jump_index: required when file has >12 entries or >100 lines
- entry_id_format: stable dot-delimited route ID

## Jump Index
- route.[route-id]:

## Routes

### route.[route-id]
- Field:
- Use when:
- Primary source docs:
- Supporting source docs:
- Validator facets:
- Trigger keywords:
- Known coverage notes:
```

### Unit Card Schema

```md
# [UNIT_ID]

path:
kind:
purpose:
open_if:
depends_on:
public_surface:
state:
promotion_trigger:
validate_fast:
validate_full:
```

### Validator Specs Directory Schema

```md
# Validator Specs

## Purpose
- route bindings source
- scoring contract
- static-first rule
- LLM gap-review rule
- source-citation rule

## Lookup Strategy
- primary_lookup: validator_route
- ordering: bound_route_id
- jump_index: required when file has >12 entries or >100 lines
- entry_id_format: validator.[route-id]

## Jump Index
- route.[route-id]:

## Shared Scoring Contract
- pass threshold:
- score formula:
- non-applicable handling:
- critical blocker overrides:
- unknown evidence handling:

## Validator Inventory
| Validator | Route | File |
|:----------|:------|:-----|
```

### Route-Bound Validator Spec Schema

```md
# [VALIDATOR_ID]

- Validator ID: `validator.[route-id]`
- Route: `route.[route-id]`
- Source docs:
- Overlay docs:
- Applies to:
- Pass threshold:

## Scoring

## Check Groups

## Static Coverage

## LLM Gap Review

## Evolution Rules
```

### Bootstrap Validator Matrix Schema

```md
# Validation Matrix

## Rules
- define proof before edit
- failed proof => unverified
- choose narrowest proof that proves the task
- replace bootstrap validators with route-bound specs when available

## [VALIDATOR_ID]
when:
command:
proves:
escalate_if:
```

### Decision Log Schema

```md
# Decision Log

| Date | Decision | Affected Nodes | Source References |
|:-----|:---------|:---------------|:------------------|
```

### Open Work Schema

```md
# Open Work

| Problem | Target Nodes | Expected End State | Removal Condition |
|:--------|:-------------|:-------------------|:------------------|
```

## Retrieval Algorithm

1. Classify task as one of: `question | edit | bug | refactor | docs | build | infra | migration | release`
2. Start at bootstrap + project foundation + repo map + route map.
3. Read declared lookup strategies before scanning cache entries.
4. Use jump indexes when present to move directly to the likely route, card group, validator, or routing child.
5. If lookup misses, use the cache miss handling path before broad rediscovery.
6. Open the minimum routing chain needed to isolate the target.
7. Open only target unit, document, or workstream cards.
8. Open validator specs only when validation, review, or enforcement is in scope.
9. Open only target source files and direct dependencies.
10. Load proof commands before declaring the plan or implementation complete.
11. Stop reading immediately when target, dependencies, and proof path are clear.

## Validation Rules

- Every task must define `validate_fast` and `validate_full` before edits begin.
- No outcome becomes `verified` until `validate_full` passes.
- Validation must prove the actual task, not only repo health.
- Always escalate validation when touching:
  - public APIs
  - schemas or migrations
  - auth or security
  - concurrency
  - caching
  - build or release logic
  - performance-sensitive paths
- If route-bound validators exist, resolve the route first, then read only the matching validator spec.
- Prefer static validators where deterministic evidence is available.
- Use LLM validation only for semantic gaps that static checks cannot judge.
- If validators are missing, create validator entries from actual repo commands before proceeding.
- In `day_zero` and `early_build`, bootstrap validators are acceptable until stronger validators exist.
- In mature repos, prefer validator specs with explicit applicability, scoring, static coverage, and critical-blocker overrides.
- If an enforcement kit exists, validate with its documented CLI or schema instead of inventing a parallel command.
- If validation fails, record blocker, keep outcome `unverified`, and do not promote the fact into durable verified memory.

Bootstrap validator examples:

- file existence checks
- manifest parse checks
- docs link checks
- repo structure checks
- smoke commands
- minimal build or test commands if they already exist

## Proof Ladder

Use the narrowest sufficient proof from this ladder, then widen only if boundary risk requires it:

- `parse/syntax`
- `lint/format check`
- `type/static analysis`
- `unit`
- `integration/contract`
- `build/package`
- `migration/data safety`
- `runtime smoke`
- `performance/hot path`

## Zero-Waste Compaction Rules

- Store pointers, not excerpts.
- One reusable fact per line.
- Prefer fixed fields over prose.
- Use repo-native IDs once, then reuse them.
- Delete rationale unless it changes a decision.
- Delete history unless it changes future routing or proof.
- Never store transcripts, logs, stack traces, generated diffs, copied source blocks, or terminal dumps in tracked shared memory.
- Never duplicate the same rule across bootstrap, shared context, and local scratch.
- Admission test for every shared-memory line:
  - `reused`
  - `expensive_to_rediscover`
  - `stable`
  - `source_linked`
  - `non_duplicative`
  - `compact`
- If any admission field fails, delete the line.

## Token Caps

Use these targets:

- always-read bundle: target `<=8k`, hard cap `12k`
- routing node: target `<=4k`, hard cap `8k`
- unit or workstream card: target `<=1.5k`, hard cap `3k`
- local scratch: no strict cap, but prune aggressively and never rely on it for shared truth

If a file exceeds its cap, shrink or split it before adding more content.

## Closeout Routing

- universal invariant -> `[BOOTSTRAP_FILE]`
- source-truth project intent change -> `[PROJECT_FOUNDATION_FILE]`
- repo-wide retrieval or workflow rule -> `llm-operating-guide.md`
- structural repo fact -> `repo-map.md`
- document/unit/workstream lookup -> `[CARD_INDEX_FILE]`
- route-selection fact -> `[ROUTING_MAP_FILE]`
- validator definition -> `[VALIDATOR_SPECS_DIR]/` or bootstrap validator matrix
- cache entry change -> update owning cache file lookup strategy, ordering, and jump index
- reusable enforcement contract -> `[ENFORCEMENT_TOOL_DIR]/`
- durable decision -> `decision-log.md`
- active unresolved blocker -> `open-work.md`
- temporary agent note -> local scratch only
- everything else -> delete

## Model Requirements

Framework deployment is model-agnostic. Any model may be used if it satisfies the requirements below.

Lead model requirements:

- strong reasoning
- strong coding
- strong tool use
- reliable long-context behavior
- preferred context `>=200k`

Support model requirements:

- cheaper and faster than lead
- sufficient context for routing and summarization
- preferred context `>=128k`

Open/private model requirements:

- use only if privacy, air-gap, or vendor-independence justifies lower peak performance
- must still obey routing, validation, and compaction rules

## Refresh Rule For Model Names

Before execution, refresh current model names from official vendor sources. Do not treat example model names below as permanent truth.

Official starting points:

- OpenAI: [Models](https://developers.openai.com/api/docs/models)
- Anthropic: [Models overview](https://platform.claude.com/docs/claude/docs/models-overview)
- Google: [Gemini models](https://ai.google.dev/gemini-api/docs/models/gemini-v2)
- Qwen: [Qwen3](https://qwenlm.github.io/blog/qwen3/) and [Qwen3-Coder](https://qwenlm.github.io/blog/qwen3-coder/)

## Example Models As Of April 25, 2026

These model names are examples only. They are informational and not required by the deployment prompt.

Lead examples:

- OpenAI: [GPT-5.5](https://openai.com/index/introducing-gpt-5-5/), [GPT-5.4](https://developers.openai.com/api/docs/models/gpt-5.4)
- Anthropic: [Claude Opus 4.7](https://platform.claude.com/docs/claude/docs/models-overview), [Claude Sonnet 4.6](https://platform.claude.com/docs/claude/docs/models-overview)
- Google: [Gemini 2.5 Pro](https://ai.google.dev/gemini-api/docs/models/gemini-v2)
- Open-weight/private: [Qwen3-Coder-480B-A35B-Instruct](https://qwenlm.github.io/blog/qwen3-coder/)

Support examples:

- OpenAI: [GPT-5.4 mini](https://openai.com/index/introducing-gpt-5-4-mini-and-nano/), [GPT-5.4 nano](https://developers.openai.com/api/docs/models/gpt-5.4-nano/)
- Anthropic: [Claude Haiku 4.5](https://platform.claude.com/docs/claude/docs/models-overview)
- Google: [Gemini 2.5 Flash](https://ai.google.dev/gemini-api/docs/models/gemini-v2), [Gemini 2.5 Flash-Lite](https://ai.google.dev/gemini-api/docs/models/gemini-v2)
- Open-weight/private: [Qwen3-235B-A22B](https://qwenlm.github.io/blog/qwen3/), [Qwen3-30B-A3B](https://qwenlm.github.io/blog/qwen3/)

## Completion Checklist

The setup is complete only if all statements are true:

- lifecycle state is chosen correctly
- minimal source truth exists
- shared context exists
- routing depth matches project size and lifecycle stage
- day_zero repos contain only top-level workstream cards, not speculative leaf cards
- existing repos preserve prior source truth unless explicitly normalized
- source-of-truth locations are explicit
- cache/index lookup strategy is declared before entries
- cache/index updates preserve declared ordering and refresh jump indexes
- cache misses fall back to source truth and backfill reusable cache entries
- validators are defined from actual repo commands or bootstrap checks
- route-bound validators are separated from source-rule text where mature enough
- enforcement tooling is reused instead of duplicated where present
- at least one cold-start read path reaches a target without broad rediscovery
- local scratch is gitignored
- tracked memory contains no transcript-like content
- token caps are respected or split actions are already applied
- shared memory is marked `verified` only where proof passed
- final validation passes
