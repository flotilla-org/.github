# flotilla-org/.github

Shared GitHub configuration for flotilla-org repositories.

## Claude code review (`.github/workflows/claude-review.yml`)

A reusable workflow (flotilla-org/flotilla#2754). A repository calls it from a short stub:

```yaml
name: Claude Code Review
on:
  pull_request:
    types: [opened, synchronize]
jobs:
  review:
    uses: flotilla-org/.github/.github/workflows/claude-review.yml@v1
    permissions:
      contents: read
      pull-requests: read
      issues: read
      id-token: write
    with:
      guidance: "the repository's CLAUDE.md"   # what the reviewer reads for conventions
      skip_label: ""                           # e.g. in-vessel-review
      model: claude-sonnet-5-5
    secrets: inherit                           # repositories outside flotilla-org pass CLAUDE_CODE_OAUTH_TOKEN explicitly
```

The reviewer judges each PR against the issue it implements and reports only bugs (a concrete failure inside that contract) and waste (code the change could do without), each numbered and marked blocking or non-blocking. It suggests no hardening, options or limits the issue did not ask for. Its comment ends with `<!-- review-findings: N -->`, the number of findings still to act on, which flotilla reads to decide whether a crew has review work (flotilla-org/flotilla#3709).

Callers pin `v1`. Releasing means moving the `v1` tag, which rolls every caller at once; try changes on one repository via `v1-canary` first. Action versions inside the workflow are pinned to commits and bumped by Dependabot.
