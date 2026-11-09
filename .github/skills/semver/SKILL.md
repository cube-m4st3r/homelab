---
name: semver
description: >-
  Commit-message rules for the homelab repository (inventory and playbooks, never released).
  Use when writing or reviewing a commit message or PR title, or when deciding how to flag a
  change that needs manual action on the hosts.
---

# Commit messages for `homelab`

This repo is **not versioned or released**: there are no tags, no release-please and no
`CHANGELOG.md`. It holds the inventory, the playbooks and the docs of a live homelab. Conventional
Commits are still used, because they keep the history readable and pre-commit enforces them.
Versioned code lives elsewhere: roles in `ansible-collection-homelab`, images in their own repos.
When a change there is needed, say so rather than working around it here.

## Commit message format

```text
<type>(<scope>)<!>: <summary>

<body: why the change was made>

BREAKING CHANGE: <what has to be done by hand on the hosts>
```

- `summary`: imperative mood, lowercase start, no trailing period, at most 72 characters.
- `scope` (optional but preferred): the app (`authentik`, `cloudflared`, `stalwart`, ...), a host name,
  or `inventory`, `playbooks`, `adr`, `ci`, `deps`.

## Types

| Type | Use for |
| --- | --- |
| `feat` | A new service, host or capability in the homelab |
| `fix` | Fixing a misconfiguration or a broken deployment |
| `perf` | Same result, less resource use |
| `docs` | README, architecture docs, ADRs (`docs(adr): ...`) |
| `refactor` | Restructuring inventory or playbooks with identical results |
| `build(deps)` | Pinning a new collection or EE version in `requirements.yml` / `ansible-navigator.yml` |
| `ci`, `chore`, `style` | CI, tooling, formatting |

## The `!` marker here

There is no release to bump, so `!` and the `BREAKING CHANGE:` footer mean something else here:
**applying this commit needs manual action**, or it causes downtime or data risk. Use it when:

- data has to be migrated, backed up or moved by hand (volume paths, database upgrades);
- a service is removed, or its hostname or URL changes;
- secrets, DNS, Cloudflare or Authentik settings have to be changed outside this repo;
- a host is renamed, re-imaged or removed;
- the runtime of a service changes (e.g. Docker → Podman Quadlet).

The footer lists the manual steps in order, so future-you can follow them.

## Procedure

1. Look at the change: `git diff --staged`.
2. Check the list above. If manual action is needed, use `!` and write the steps into the footer.
3. When unsure whether a change affects running services, **ask** instead of guessing.
4. One logical change per commit. Suggest splitting unrelated changes.
5. If the change also needs a new ADR, mention it.

## Examples

```text
feat(cloudflared): deploy tunnel as rootless quadlet on apps01
```

```text
fix(authentik): publish ldaps on 6636 instead of privileged 636
```

```text
feat(authentik)!: migrate from docker compose to podman quadlets

BREAKING CHANGE: before applying, stop the compose stack and back up
the postgres volume. Restore it into the new `authentik-db` volume,
then run playbooks/apps/authentik.yml.
```
