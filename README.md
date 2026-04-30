# Hybrid LLM Operating Framework

Use this folder as a standalone setup kit for adding durable, low-token, validation-first LLM operating context to a new or existing repository.

## Files

- `Hybrid_LLM_Operating_Framework_Setup_Spec.md`: framework contract, lifecycle model, schemas, routing rules, validation rules, and compaction rules.
- `README.md`: setup instructions and the deployment prompt.

## Set Up A Project

1. Make this folder available to the LLM that will update the target repository.
2. Start a capable coding agent with filesystem access to the target repository.
3. Give it the deployment prompt below.
4. Set `repo_path` to the target repository path.
5. Set `framework_doc_path` to `Hybrid_LLM_Operating_Framework_Setup_Spec.md` in this folder.
6. Use `mode: auto` unless you already know the target is `new` or `existing`.
7. Provide `project_intent` only when the target repository is empty or has no reliable README, docs, manifest, or starter skeleton.
8. Let the agent discover placeholders live, merge with existing guidance, create missing framework files, run validation, and summarize the result.

## Use After Setup

- Start future LLM sessions from the target repository bootstrap file and shared context read order.
- Treat source files, canonical docs, schemas, manifests, and deployment config as source truth.
- Treat shared context as routing and reusable memory, not copied source content.
- Keep local scratch files gitignored and disposable.
- Update shared context only for stable facts that save future rediscovery.
- Run the target repository validation before marking setup facts `verified`.

## Deployment Prompt

Inputs:

- `repo_path`: target repository path
- `framework_doc_path`: path to `Hybrid_LLM_Operating_Framework_Setup_Spec.md`
- `mode`: `auto | new | existing`
- `preferred_bootstrap_filename`: optional
- `project_intent`: optional; use only for empty repositories with no reliable source truth

Copy/paste prompt:

```text
You are deploying the Hybrid LLM Operating Framework in the repository at [repo_path].

Read the full framework setup spec at [framework_doc_path] before writing files. Treat that spec as the operating contract for lifecycle detection, source-truth selection, target topology, schemas, routing, validators, validation, and compaction.

Inputs:
- repo_path: [absolute or repo-relative path]
- framework_doc_path: [absolute or repo-relative path]
- mode: [auto|new|existing]
- preferred_bootstrap_filename: [value or auto]
- project_intent: [text or empty]

Your job is to set up a reusable, low-token, validation-first operating framework that works from day-zero through mature-repo stages.

Execution rules:
1. Detect repository state first: day_zero, early_build, active_growth, or mature_repo.
2. If mode is explicit, honor it unless repo reality makes it impossible; if impossible, explain the mismatch and use the closest valid branch.
3. Discover existing source truth, guidance files, shared context, route maps, validator specs, enforcement tools, validation commands, and gitignore patterns before creating anything.
4. For day_zero repos, create or confirm one minimal canonical project-intent source document before shared context setup. Use this order:
   - existing root README
   - existing docs index, ADR, or architecture overview
   - new docs/project-foundation.md if nothing adequate exists
5. Preserve source-truth files. Merge guidance when possible. Do not overwrite blindly.
6. Reuse existing cache shapes when present: repo map, card index, route map, validator specs, decisions, open work, and enforcement kit metadata.
7. Declare lookup strategy before entries in every repo map, card index, route map, validator index, and large routing node.
8. For existing cache files, normalize missing lookup strategies before building, backfilling, or updating entries.
9. When updating shared cache entries, preserve declared ordering and refresh jump indexes in the same edit.
10. If cache lookup misses, fall back to source truth; never treat cache absence as proof that a target does not exist.
11. Create missing framework files only as needed for the current lifecycle stage.
12. Start tiny. Do not fabricate deep routing or hypothetical leaf units.
13. In day_zero or early_build, create top-level workstream cards first. Add unit cards only when a real unit or approved boundary exists.
14. Keep local scratch gitignored and out of tracked shared context.
15. Define validators before marking anything verified. Use bootstrap validators if real validators do not yet exist.
16. If route-bound validator specs exist, bind validators to routes and keep scoring/facets separate from source-rule text.
17. If a reusable enforcer exists, document its CLI/config/schema/report contract instead of duplicating it in shared memory.
18. Mark planned structures as planned or active. Mark facts verified only when source-backed and validated.
19. Ask a question only if a high-impact value cannot be discovered and cannot be safely assumed.
20. Stop only after setup is complete, validated, and summarized.

Required outputs:
- created and updated files
- project state chosen and why
- routing depth chosen and why
- source-truth files reused or created
- shared cache files reused or created
- existing cache normalization performed or skipped
- cache lookup strategy applied or refreshed
- cache misses and backfills, if any
- validators added or reused
- enforcement tool added or reused, if applicable
- planned items vs verified items
- validation commands run and results
- remaining open work

Hard prohibitions:
- do not treat chat history as source truth
- do not store transcripts or terminal dumps in shared context
- do not create speculative deep routing
- do not mark unproven facts as verified
- do not duplicate source-rule bodies into shared memory
- do not overwrite existing project guidance without preserving or merging useful content
```
