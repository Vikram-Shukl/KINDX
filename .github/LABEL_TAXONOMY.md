# KINDX Label Taxonomy

This document defines the maintainer-facing label model that pairs with the redesigned issue templates.

The goal is to keep existing public labels working while giving maintainers a more expressive taxonomy for triage, contributor routing, and release planning.

## Preserve Existing Public Labels

Keep using these labels where they already map well to the issue chooser and contributor expectations:

- `bug`
- `documentation`
- `enhancement`
- `performance`
- `architecture`
- `epic`
- `good first issue`
- `help wanted`
- `triage`
- `discussion`
- `search-quality`
- `embedding`

## Proposed Maintainer Taxonomy

These labels are designed to be additive. They do not replace the existing public labels immediately; they refine them.

### `type/*`

Use one primary type label per issue:

- `type/bug`
- `type/documentation`
- `type/feature`
- `type/performance`
- `type/architecture`
- `type/epic`
- `type/testing`
- `type/ci`
- `type/tooling`

Suggested mapping to current labels:

- `type/bug` -> keep `bug`
- `type/documentation` -> keep `documentation`
- `type/feature` -> keep `enhancement`
- `type/performance` -> keep `performance`
- `type/architecture` -> keep `architecture`
- `type/epic` -> keep `epic`

### `area/*`

Use one or two area labels to show the primary surfaces involved:

- `area/docs`
- `area/cli`
- `area/config`
- `area/indexing`
- `area/search`
- `area/mcp`
- `area/remote-backend`
- `area/packaging`
- `area/testing`
- `area/ci`
- `area/cross-cutting`

### `difficulty/*`

Use exactly one contributor-difficulty label:

- `difficulty/good-first`
- `difficulty/beginner`
- `difficulty/intermediate`
- `difficulty/advanced`

Template mapping:

- `Good First Issue` -> `difficulty/good-first`
- `Beginner Issue` -> `difficulty/beginner`
- `Intermediate Issue` -> `difficulty/intermediate`
- `Advanced Issue` -> `difficulty/advanced`

### `status/*`

Use one workflow label to show the maintainer state:

- `status/triage`
- `status/ready`
- `status/needs-design`
- `status/blocked`
- `status/in-progress`
- `status/review-needed`

Recommended transition:

1. New issue lands with `triage`
2. Maintainer reviews and adds `status/ready` or `status/needs-design`
3. Contributor starts work -> `status/in-progress`
4. PR opens or follow-up needed -> `status/review-needed`

The repository automation behind `.github/workflows/issue-commands.yml` can apply `status/in-progress` when a contributor comments `/working` on an issue.

## Template-to-Label Guidance

The redesigned issue templates intentionally keep current public labels in their front matter for compatibility:

- `Good First Issue` -> `good first issue`, `triage`
- `Beginner Issue` -> `help wanted`, `triage`
- `Intermediate Issue` -> `help wanted`, `triage`
- `Advanced Issue` -> `help wanted`, `triage`
- `Bug Report` -> `bug`, `triage`
- `Documentation` -> `documentation`, `triage`
- `Feature Request` -> `enhancement`, `triage`
- `Performance Improvement` -> `performance`, `triage`
- `Architecture Proposal` -> `architecture`, `discussion`
- `Epic / Tracking Issue` -> `epic`, `triage`

Maintainers can then add the `type/*`, `area/*`, `difficulty/*`, and `status/*` labels manually or via future automation.
