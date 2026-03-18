# KINDX Issue Drafts

These draft issues are grounded in the current repository state as of 2026-03-17.

Before drafting, the candidate themes were re-checked against:

- Open issues `#12` through `#29`
- Recently closed issues `#11` and `#35`

The drafts below intentionally avoid topics that are already open, already closed, or already implemented on `main`, including the current roadmap work around live indexing, cold-start elimination, semantic cache, low-VRAM handling, CJK BM25, feedback loops, multimodal indexing, index history, contextual chunking, capability manifests, SDKs, and named-index isolation.

Each issue mirrors the redesigned template field order:

1. Type
2. Area
3. Difficulty
4. Maintainer Guidance Needed
5. Problem
6. Why It Matters
7. Scope
8. Acceptance Criteria
9. Relevant Files
10. Testing
11. Non-goals
12. Difficulty Rationale
13. Checklist

The difficulty templates now also include contributor-profile guidance, issue-claiming expectations, typical scope, and fit criteria. This draft file stays focused on the fillable issue content so maintainers can copy the structured sections directly when creating issues.

---

## Good First Issues

### Good First Issue: Audit README environment variable table against code defaults and undocumented variables

Recommended labels: `good first issue`, `documentation`, `triage`, `type/documentation`, `area/docs`, `area/config`, `difficulty/good-first`, `status/ready`

#### Type
Documentation

#### Area
Documentation

#### Difficulty
Good first issue

#### Maintainer Guidance Needed
Low

#### Problem
The README environment-variable table is out of sync with the implementation. It currently documents some variables but misses others that are already used in code, and at least one documented default is incorrect.

#### Why It Matters
Operators and contributors rely on the README as the first source of truth for runtime configuration. When the table is incomplete or wrong, troubleshooting becomes slower and users miss supported controls such as CPU-only mode, daemon auth, and rerank tuning.

#### Scope
Audit the environment variables used in `engine/inference.ts`, `engine/protocol.ts`, `engine/watcher.ts`, `engine/catalogs.ts`, and `engine/repository.ts`, then update the README table so it reflects the current shipped behavior and defaults.

#### Acceptance Criteria
- [ ] The README environment-variable table matches the variables currently read by the codebase.
- [ ] The documented default for `KINDX_OPENAI_GENERATE_MODEL` matches `engine/remote-llm.ts`.
- [ ] Missing user-facing variables such as `KINDX_RERANK_MODEL`, `KINDX_RERANK_CONTEXT_SIZE`, `KINDX_CPU_ONLY`, and `KINDX_MCP_TOKEN` are documented.
- [ ] Test-only or internal-only variables are either omitted intentionally or clearly marked as such.

#### Relevant Files
- `README.md`
- `engine/inference.ts`
- `engine/remote-llm.ts`
- `engine/protocol.ts`
- `engine/watcher.ts`
- `engine/repository.ts`

#### Testing
- Compare the final README table against the code paths that read `process.env.*`.
- Run `npm run build` to confirm the repo still builds after the doc update.

#### Non-goals
- Adding new environment variables
- Changing runtime behavior
- Writing a full deployment guide for every backend

#### Difficulty Rationale
This is a focused documentation audit with a clear source of truth in code and no schema or runtime changes.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] This task is small, scoped, and realistic for a first contribution.
- [x] The likely files and verification steps are listed.

### Good First Issue: Fix the broken Bun preload path in `specs/smoke-install.sh`

Recommended labels: `good first issue`, `triage`, `type/testing`, `area/packaging`, `area/testing`, `difficulty/good-first`, `status/ready`

#### Type
Testing

#### Area
Packaging and install workflows

#### Difficulty
Good first issue

#### Maintainer Guidance Needed
Low

#### Problem
`specs/smoke-install.sh` references `./engine/test-preload.ts` for the Bun smoke test, but that file does not exist in the repository. The actual preload helper is `engine/preloader.ts`.

#### Why It Matters
The smoke-install script is supposed to catch packaging and runtime regressions. A broken preload reference weakens that safety net and makes the Bun path harder to trust or revive in CI later.

#### Scope
Update the smoke-install pathing so the Bun test references the correct preload helper, or introduce a correctly named preload file if that is the better long-term shape. Keep the script and repo layout consistent.

#### Acceptance Criteria
- [ ] `specs/smoke-install.sh` no longer references a missing preload file.
- [ ] The preload helper path used by the Bun smoke test matches a real file in the repo.
- [ ] The chosen preload filename is consistent with other repo references.

#### Relevant Files
- `specs/smoke-install.sh`
- `engine/preloader.ts`
- `specs/Containerfile`

#### Testing
- Run a focused command path from `specs/smoke-install.sh` or verify the Bun command string resolves a real file.
- Run `npm run build` to confirm the repo is still healthy.

#### Non-goals
- Redesigning the smoke-install workflow
- Adding a full CI packaging job
- Refactoring the entire preload/disposal strategy

#### Difficulty Rationale
This is a tight, single-surface fix with a clear mismatch between script and filesystem state.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] This task is small, scoped, and realistic for a first contribution.
- [x] The likely files and verification steps are listed.

### Good First Issue: Document existing YAML `ignore` patterns in `README.md`

Recommended labels: `good first issue`, `documentation`, `triage`, `type/documentation`, `area/docs`, `area/config`, `difficulty/good-first`, `status/ready`

#### Type
Documentation

#### Area
Configuration and YAML catalogs

#### Difficulty
Good first issue

#### Maintainer Guidance Needed
Low

#### Problem
The repository already supports collection-level `ignore` patterns in YAML and tests that behavior, but the README does not explain the feature or show how to configure it.

#### Why It Matters
Users editing `index.yml` manually have no discoverable documentation for a supported capability. That creates unnecessary trial-and-error and makes the feature look unofficial.

#### Scope
Document the `ignore` array in the README, including where it lives in `index.yml`, how it interacts with `pattern`, and a short example of excluding directories such as session archives.

#### Acceptance Criteria
- [ ] The README includes a concrete YAML example showing `ignore` patterns.
- [ ] The docs explain that `ignore` is configured in YAML today.
- [ ] The docs make it clear that ignored files are skipped during indexing and search.

#### Relevant Files
- `README.md`
- `engine/catalogs.ts`
- `specs/command-line.test.ts`

#### Testing
- Cross-check the README example against the behavior already covered by the ignore-pattern tests.
- Run `npm run build` after the docs update.

#### Non-goals
- Adding CLI commands for ignore-pattern management
- Changing ignore-pattern semantics
- Reworking collection config format

#### Difficulty Rationale
This is a documentation-only issue with existing code and tests already serving as a reference implementation.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] This task is small, scoped, and realistic for a first contribution.
- [x] The likely files and verification steps are listed.

### Good First Issue: Expose existing but hidden CLI options in help/docs (`collection add --mask` and `get --from`)

Recommended labels: `good first issue`, `documentation`, `triage`, `type/documentation`, `area/cli`, `area/docs`, `difficulty/good-first`, `status/ready`

#### Type
Documentation

#### Area
CLI interface and commands

#### Difficulty
Good first issue

#### Maintainer Guidance Needed
Low

#### Problem
The CLI parser supports `collection add --mask` and `get --from`, but the main help output does not surface them clearly. This leaves supported options effectively hidden unless someone reads the code or tests.

#### Why It Matters
Help text should reflect the real command surface. Hidden supported flags increase support burden and create avoidable confusion for users trying to discover documented workflows.

#### Scope
Update `showHelp()` and the related README command sections so `collection add --mask` and `get --from` are visible anywhere a user would reasonably look for command usage.

#### Acceptance Criteria
- [ ] `kindx --help` exposes `collection add --mask`.
- [ ] `kindx --help` exposes `get --from`.
- [ ] README command documentation reflects the same options and naming.

#### Relevant Files
- `engine/kindx.ts`
- `README.md`
- `specs/command-line.test.ts`

#### Testing
- Run `kindx --help` locally and verify both options appear.
- Add or update a focused command-line test if needed.
- Run `npm run build`.

#### Non-goals
- Adding new CLI flags
- Renaming existing flags
- Redesigning the overall help layout

#### Difficulty Rationale
The behavior already exists; the issue is discoverability and help-text parity.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] This task is small, scoped, and realistic for a first contribution.
- [x] The likely files and verification steps are listed.

### Good First Issue: Add regression tests for `collection include` / `exclude` default-query behavior

Recommended labels: `good first issue`, `triage`, `type/testing`, `area/config`, `area/testing`, `difficulty/good-first`, `status/ready`

#### Type
Testing

#### Area
Configuration and YAML catalogs

#### Difficulty
Good first issue

#### Maintainer Guidance Needed
Low

#### Problem
The CLI implements `collection include` / `exclude` by toggling `includeByDefault`, but the command-line tests do not currently prove that default queries actually honor the setting end to end.

#### Why It Matters
This is exactly the kind of behavior that can regress quietly during refactors. A missing regression test leaves a contributor-facing feature underprotected.

#### Scope
Add focused command-line tests that verify excluded collections are omitted from default queries, re-included collections are searched again, and explicit `--collection` filters still override the default behavior.

#### Acceptance Criteria
- [ ] A default query ignores a collection after `kindx collection exclude <name>`.
- [ ] A default query includes that collection again after `kindx collection include <name>`.
- [ ] An explicit `--collection <name>` query still reaches the excluded collection when asked directly.

#### Relevant Files
- `specs/command-line.test.ts`
- `engine/kindx.ts`
- `engine/catalogs.ts`

#### Testing
- Run the focused command-line tests you add.
- Run `npm run build`.

#### Non-goals
- Changing include/exclude behavior
- Adding new collection metadata
- Redesigning query filtering

#### Difficulty Rationale
This is a well-bounded testing issue with existing commands and behavior already in place.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] This task is small, scoped, and realistic for a first contribution.
- [x] The likely files and verification steps are listed.

---

## Beginner Issues

### Beginner Issue: Add `--pattern` as an alias for `collection add --mask` and align terminology

Recommended labels: `help wanted`, `enhancement`, `triage`, `type/feature`, `area/cli`, `area/config`, `difficulty/beginner`, `status/ready`

#### Type
CLI / UX enhancement

#### Area
CLI interface and commands

#### Difficulty
Beginner

#### Maintainer Guidance Needed
Medium

#### Problem
The CLI flag is named `--mask`, while the config model, README prose, and user mental model mostly talk about glob `pattern`s. That mismatch makes the surface harder to learn and document consistently.

#### Why It Matters
Terminology drift between CLI, config, and docs creates ongoing confusion and increases the chance of future documentation skew.

#### Scope
Add `--pattern` as a backward-compatible alias for `collection add --mask`, then update help text and docs so the preferred terminology is consistent across the product.

#### Acceptance Criteria
- [ ] `kindx collection add <path> --pattern "**/*.md"` works.
- [ ] Existing `--mask` behavior remains supported.
- [ ] The help text and README present the alias clearly and avoid contradictory terminology.
- [ ] A command-line test covers both spellings.

#### Relevant Files
- `engine/kindx.ts`
- `README.md`
- `specs/command-line.test.ts`

#### Testing
- Add or update focused CLI tests covering `--pattern` and `--mask`.
- Run `npm run build`.
- Run the affected `specs/command-line.test.ts` cases.

#### Non-goals
- Removing `--mask`
- Renaming the YAML `pattern` field
- Redesigning collection-add parsing beyond this alias

#### Difficulty Rationale
This touches CLI parsing, docs, and tests, but the behavior change is small and backward-compatible.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] The issue is scoped to one focused PR.
- [x] The likely files and verification steps are listed.

### Beginner Issue: Support `~` expansion for YAML-defined collection paths and update `sample-catalog.yml`

Recommended labels: `help wanted`, `bug`, `triage`, `type/bug`, `area/config`, `area/indexing`, `difficulty/beginner`, `status/ready`

#### Type
Small behavior change

#### Area
Configuration and YAML catalogs

#### Difficulty
Beginner

#### Maintainer Guidance Needed
Medium

#### Problem
`sample-catalog.yml` shows collection paths like `~/Documents/Meetings`, but YAML-defined collection paths are not expanded from `~` to the user’s home directory. In practice, unresolved `~` values fall back to cwd-relative resolution when the path does not exist.

#### Why It Matters
The sample config currently teaches a shape that is misleading in real use. That creates broken configs for users who edit `index.yml` manually instead of using the CLI.

#### Scope
Expand leading `~` in YAML-defined collection paths before the paths are consumed by the rest of the system, and update `sample-catalog.yml` or related docs so the example matches the supported behavior.

#### Acceptance Criteria
- [ ] A collection path beginning with `~/` resolves to the user’s home directory when loaded from YAML.
- [ ] Existing absolute and relative path behavior remains unchanged.
- [ ] `sample-catalog.yml` reflects the supported behavior.
- [ ] A focused test covers YAML path expansion.

#### Relevant Files
- `engine/catalogs.ts`
- `engine/repository.ts`
- `engine/kindx.ts`
- `sample-catalog.yml`
- `specs/catalogs-config.test.ts`

#### Testing
- Add a focused config/path test for `~/...` collection paths.
- Run `npm run build`.
- Run the affected catalog or path-related tests.

#### Non-goals
- Full shell expansion for arbitrary env vars
- Changing how globs work
- Rewriting path resolution across unrelated CLI entry points

#### Difficulty Rationale
The issue spans config loading and path consumers, but the expected behavior is straightforward and testable.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] The issue is scoped to one focused PR.
- [x] The likely files and verification steps are listed.

### Beginner Issue: Refresh the README data-storage/schema documentation to match the current SQLite + YAML design

Recommended labels: `help wanted`, `documentation`, `triage`, `type/documentation`, `area/docs`, `area/config`, `difficulty/beginner`, `status/ready`

#### Type
Documentation

#### Area
Documentation

#### Difficulty
Beginner

#### Maintainer Guidance Needed
Medium

#### Problem
The README schema diagram is stale relative to the current implementation. It still implies structures like a `collections` table and outdated document fields, while the real system stores collection config in YAML and uses separate `content`, `documents`, `content_vectors`, and `llm_cache` tables with different column names.

#### Why It Matters
Architecture docs should help contributors orient themselves. A stale schema sends people to the wrong mental model before they ever open `engine/repository.ts`.

#### Scope
Update the README data-storage section and schema diagram so it accurately reflects the current storage model, including the role of YAML config versus SQLite tables.

#### Acceptance Criteria
- [ ] The README schema description matches the current tables created in `engine/repository.ts`.
- [ ] The docs no longer imply that collection config lives in SQLite.
- [ ] The data-storage section mentions the YAML catalog as part of the system design.

#### Relevant Files
- `README.md`
- `engine/repository.ts`
- `engine/catalogs.ts`

#### Testing
- Cross-check the updated README against the schema creation code in `engine/repository.ts`.
- Run `npm run build`.

#### Non-goals
- Redesigning the database schema
- Adding new migration docs
- Writing a full low-level schema reference for every table and index

#### Difficulty Rationale
This is documentation work, but it requires enough repo familiarity to reconcile docs with the live schema and config model.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] The issue is scoped to one focused PR.
- [x] The likely files and verification steps are listed.

### Beginner Issue: Add a remote-backend quickstart for Ollama, LM Studio, and OpenAI-compatible endpoints

Recommended labels: `help wanted`, `documentation`, `triage`, `type/documentation`, `area/docs`, `area/remote-backend`, `difficulty/beginner`, `status/ready`

#### Type
Documentation

#### Area
Remote backend and model integration

#### Difficulty
Beginner

#### Maintainer Guidance Needed
Medium

#### Problem
The repository supports a `remote` LLM backend and even has tests covering remote embedding/query flow, but the README only exposes environment variables. There is no quickstart showing how to point KINDX at Ollama, LM Studio, or another OpenAI-compatible endpoint.

#### Why It Matters
Without a quickstart, users cannot easily discover or trust a major fallback path for machines where local model execution is not desirable or not possible.

#### Scope
Add a short remote-backend guide to the README that covers backend selection, the required environment variables, optional API keys, and how rerank fallback behaves when `/v1/rerank` is unavailable.

#### Acceptance Criteria
- [ ] The README shows a minimal remote-backend setup example.
- [ ] The docs mention `KINDX_LLM_BACKEND=remote` and the required endpoint variables.
- [ ] The docs explain the current rerank fallback behavior for backends without `/v1/rerank`.
- [ ] The examples are consistent with the existing remote-backend tests.

#### Relevant Files
- `README.md`
- `engine/remote-llm.ts`
- `specs/command-line.test.ts`

#### Testing
- Cross-check the docs against `engine/remote-llm.ts` defaults and behavior.
- Run `npm run build`.

#### Non-goals
- Adding new remote-backend features
- Implementing provider-specific adapters
- Writing a full hosted deployment guide

#### Difficulty Rationale
This is still documentation work, but it requires reading the remote backend implementation and tests closely enough to avoid inaccurate examples.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] The issue is scoped to one focused PR.
- [x] The likely files and verification steps are listed.

---

## Intermediate Issues

### Intermediate Issue: Add CLI support for managing collection ignore patterns

Recommended labels: `help wanted`, `enhancement`, `triage`, `type/feature`, `area/cli`, `area/config`, `difficulty/intermediate`, `status/ready`

#### Type
Feature work

#### Area
Configuration and YAML catalogs

#### Difficulty
Intermediate

#### Maintainer Guidance Needed
Medium

#### Problem
Collection `ignore` patterns are supported in YAML and respected during indexing, but there is no first-class CLI for adding, removing, or inspecting them beyond manual file edits and `collection list` output.

#### Why It Matters
Manual YAML edits are error-prone and inconsistent with the otherwise strong CLI-first collection-management story in KINDX.

#### Scope
Introduce a small CLI surface for managing ignore patterns on existing collections while preserving backward compatibility with manual YAML configuration.

#### Acceptance Criteria
- [ ] The CLI supports adding at least one ignore pattern to an existing collection.
- [ ] The CLI supports removing or clearing previously configured ignore patterns.
- [ ] Existing YAML-defined ignore patterns remain supported.
- [ ] Help text, docs, and tests cover the new workflow.

#### Relevant Files
- `engine/kindx.ts`
- `engine/catalogs.ts`
- `README.md`
- `specs/command-line.test.ts`

#### Testing
- Add focused command-line tests for the new ignore-pattern commands.
- Run `npm run build`.
- Run the affected command-line tests.

#### Non-goals
- Reworking the ignore glob engine
- Adding per-command temporary ignore overrides
- Replacing manual YAML editing entirely

#### Difficulty Rationale
This needs coordinated CLI, config, documentation, and test changes, but the feature boundary is still narrow and contributor-ready.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] The issue is scoped to one focused PR.
- [x] The likely files and verification steps are listed.

### Intermediate Issue: Validate `index.yml` on load, including bad types, unknown keys, and legacy array-shaped collections

Recommended labels: `help wanted`, `enhancement`, `triage`, `type/tooling`, `area/config`, `area/testing`, `difficulty/intermediate`, `status/ready`

#### Type
Configuration improvement

#### Area
Configuration and YAML catalogs

#### Difficulty
Intermediate

#### Maintainer Guidance Needed
High

#### Problem
`loadConfig()` currently parses YAML and casts it directly to `CollectionConfig` without structural validation. That leaves room for silent misconfiguration, confusing runtime behavior, and legacy array-shaped `collections` values that some helpers partly support and others do not.

#### Why It Matters
Configuration errors should fail fast and clearly. Right now, a malformed `index.yml` can surface much later as odd behavior in listing, path resolution, or default-collection selection.

#### Scope
Add schema validation for `index.yml` on load, with explicit error messages for invalid shapes, unknown keys, wrong types, and legacy array-style collection configs. Preserve the current object-based config format as the supported shape.

#### Acceptance Criteria
- [ ] Invalid top-level config structures fail with clear, actionable error messages.
- [ ] Unknown keys are surfaced clearly rather than ignored silently.
- [ ] Legacy array-shaped `collections` values no longer produce ambiguous downstream behavior.
- [ ] Focused tests cover valid config, invalid types, unknown keys, and legacy array input.

#### Relevant Files
- `engine/catalogs.ts`
- `specs/catalogs-config.test.ts`
- `CONTRIBUTING.md` or `README.md` if the supported config shape is clarified

#### Testing
- Add focused config-validation tests.
- Run `npm run build`.
- Run the affected catalog/config tests.

#### Non-goals
- A full auto-migration tool for every historical config shape
- Redesigning the config format
- Adding interactive config editing

#### Difficulty Rationale
This is cross-cutting config hardening work that needs careful compatibility judgment and good test coverage.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] The issue is scoped to one focused PR.
- [x] The likely files and verification steps are listed.

### Intermediate Issue: Add a CI packaging smoke job that exercises install/runtime smoke checks

Recommended labels: `help wanted`, `enhancement`, `triage`, `type/ci`, `area/ci`, `area/packaging`, `difficulty/intermediate`, `status/ready`

#### Type
Tooling / CI

#### Area
CI/CD and automation

#### Difficulty
Intermediate

#### Maintainer Guidance Needed
Medium

#### Problem
The repo already contains `specs/smoke-install.sh` and `specs/Containerfile`, but no GitHub Actions job currently exercises that packaging/install smoke coverage. CI stops at `npm test` and `npm pack --dry-run`.

#### Why It Matters
Install and packaging regressions can still slip through even when unit and integration tests pass. The recently closed install issue shows why that gap matters.

#### Scope
Add a CI job or workflow that runs a practical subset of the packaging smoke checks, using the existing container/script assets rather than inventing a separate parallel harness.

#### Acceptance Criteria
- [ ] GitHub Actions runs a packaging/install smoke path in CI.
- [ ] The smoke path validates at least one install/runtime flow beyond `npm pack --dry-run`.
- [ ] The workflow fails clearly when install/runtime smoke checks fail.
- [ ] The workflow documentation or comments explain how to run the same smoke checks locally.

#### Relevant Files
- `.github/workflows/ci.yml`
- `specs/smoke-install.sh`
- `specs/Containerfile`

#### Testing
- Validate the workflow YAML syntax locally if possible.
- Run the relevant smoke-install steps locally or document any environment limitation.
- Run `npm run build`.

#### Non-goals
- Redesigning every GitHub Actions workflow
- Shipping a full release pipeline rewrite
- Replacing the existing smoke-install script

#### Difficulty Rationale
This spans workflows, packaging smoke scripts, and containerized verification, so it needs more context than a starter task but is still well-bounded.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] The issue is scoped to one focused PR.
- [x] The likely files and verification steps are listed.

---

## Advanced Issues

### Advanced Issue: Make model-backed tests capability-aware so unsupported local Metal/GPU/model contexts skip cleanly outside CI

Recommended labels: `help wanted`, `enhancement`, `triage`, `type/testing`, `area/testing`, `area/remote-backend`, `difficulty/advanced`, `status/ready`

#### Type
Testing infrastructure

#### Area
Testing infrastructure

#### Difficulty
Advanced

#### Maintainer Guidance Needed
High

#### Problem
The model-backed test suites currently skip only when `CI` is set. On local machines without usable `node-llama-cpp` runtime capability, the tests still run and fail noisily with Metal/context initialization errors even though the rest of the repo is healthy.

#### Why It Matters
That makes local contributor feedback noisy and brittle, especially on machines that cannot allocate working model contexts. It also hides the difference between “repo broken” and “environment incapable.”

#### Scope
Add a reusable capability probe for model-backed tests so they can skip with a clear reason when the machine cannot create the required local contexts, while keeping ordinary unit tests and CI behavior intact.

#### Acceptance Criteria
- [ ] Local model-backed tests detect capability before running long integration paths.
- [ ] Unsupported environments skip with a clear, actionable reason instead of failing late with raw runtime errors.
- [ ] The skip logic is shared instead of duplicated ad hoc across multiple spec files.
- [ ] CI semantics stay explicit and do not silently mask real regressions.

#### Relevant Files
- `specs/inference.test.ts`
- `specs/store.test.ts`
- `specs/mcp.test.ts`
- `engine/preloader.ts`
- Any new shared spec helper introduced for capability checks

#### Testing
- Verify the capability probe on a machine that cannot initialize local contexts.
- Verify that non-model unit tests still run normally.
- Run `npm run build`.

#### Non-goals
- Solving low-VRAM or Metal backend limitations themselves
- Replacing local model integration with mocks
- Reworking the entire test strategy for the repository

#### Difficulty Rationale
This requires careful judgment about what counts as “capable enough,” how to preserve meaningful coverage, and how not to hide real failures behind overly broad skips.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] The key scope boundaries, likely files, and verification approach are listed.
- [x] This issue is design-complete enough to implement without a separate architecture proposal.

### Advanced Issue: Add `kindx doctor` to report sqlite-vec, backend mode, remote API config, MCP auth, and daemon/watch health

Recommended labels: `help wanted`, `enhancement`, `triage`, `type/feature`, `area/cli`, `area/cross-cutting`, `difficulty/advanced`, `status/ready`

#### Type
Diagnostics and developer tooling

#### Area
Cross-cutting / multiple subsystems

#### Difficulty
Advanced

#### Maintainer Guidance Needed
High

#### Problem
Troubleshooting KINDX is currently spread across `kindx status`, warning messages, README environment-variable docs, and implementation details in multiple files. There is no single diagnostics command that tells an operator whether sqlite-vec is available, which backend is active, whether auth is configured, and whether the daemon/watch processes are healthy.

#### Why It Matters
Support and onboarding are slower without a single diagnostics entry point. A read-only doctor command would make local setup, issue triage, and deployment debugging much easier.

#### Scope
Add a `kindx doctor` command that prints a read-only diagnostics report covering sqlite-vec availability, local vs remote backend mode, safe remote-config presence checks, MCP auth configuration, and daemon/watch status.

#### Acceptance Criteria
- [ ] `kindx doctor` exists as a documented CLI command.
- [ ] The output reports sqlite-vec availability and any obvious extension-loading limitation.
- [ ] The output reports the current backend mode and relevant non-secret config hints.
- [ ] The output reports MCP daemon/watch health similarly to existing status checks.
- [ ] Focused tests cover the command shape and at least the core report sections.

#### Relevant Files
- `engine/kindx.ts`
- `engine/protocol.ts`
- `engine/inference.ts`
- `engine/repository.ts`
- `README.md`
- `specs/command-line.test.ts`

#### Testing
- Add focused CLI tests for `kindx doctor`.
- Validate the command on both local-backend and remote-backend configurations if possible.
- Run `npm run build`.

#### Non-goals
- Auto-fixing broken environments
- Printing secrets or full auth tokens
- Replacing `kindx status`

#### Difficulty Rationale
This is a cross-cutting diagnostics feature that touches several subsystems and needs careful UX, security, and testing choices.

#### Checklist
- [x] I searched open and recently closed issues before drafting this task.
- [x] The key scope boundaries, likely files, and verification approach are listed.
- [x] This issue is design-complete enough to implement without a separate architecture proposal.
