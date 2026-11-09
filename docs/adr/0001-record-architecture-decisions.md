# 0001. Record architecture decisions

- Status: accepted
- Date: 2026-11-09

## Context

This homelab is rebuilt in public, spread over several repositories (homelab, an Ansible collection, a handful of
container images). Many choices are made along the way: which container runtime, how repos are split, how changes
reach the hosts. The code shows *what* was built, but not *why*, or what was considered and rejected.

Two audiences need that *why*:

- **Future me**, who will forget the reasoning within months and then either repeat the analysis or undo a decision
  without knowing why it was made.
- **Visitors**, who want to adapt the setup to their own lab. For them the reasoning is more useful than the YAML.

## Decision

Significant decisions are recorded as Architecture Decision Records (ADRs), following
[Michael Nygard's format](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions):

- One Markdown file per decision in `docs/adr/`, named `NNNN-short-title.md` with a sequential four-digit number.
- Each ADR has a **status** (`proposed`, `accepted`, `superseded by NNNN`), a **date**, and three sections:
  **Context**, **Decision** and **Consequences**. An **Options considered** section is added when there were real alternatives.
- New ADRs start from [`template.md`](template.md).
- **Accepted ADRs are not rewritten.** When a decision changes, a new ADR supersedes the old one, and the old one only
  gets its status updated. The history of the reasoning stays visible.
- ADRs that affect the whole project (including the other repositories) live here in `homelab`, which is the
  project's entry point.

Rule of thumb for when to write one: the decision took more than about ten minutes, or someone could reasonably have
chosen differently.

## Consequences

- Every bigger change comes with a short write-up. It costs a few minutes per decision.
- The README can link here instead of explaining every choice inline.
- Superseded ADRs stay in the folder. Readers have to check the status line.
- The ADR log only works if it is kept up to date. Adding an ADR is part of the change itself, in the same pull request.
