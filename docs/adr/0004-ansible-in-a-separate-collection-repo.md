# 0004. Ansible roles live in a separate collection repository

- Status: accepted
- Date: 2026-11-09

## Context

The homelab is deployed with Ansible. There are two kinds of Ansible content:

- **What runs where:** inventory, host and group vars, playbooks. Specific to this homelab.
- **How things are deployed:** roles that install Podman, generate Quadlet files and set up apps like Authentik.
  Generic enough for other people's labs.

Keeping both in one repo is the simplest start, but mixes something reusable with something personal. Nobody else can
consume the roles without copying them.

## Options considered

1. **Everything in the `homelab` repo** (`roles/` next to `playbooks/`). Simple, one PR per change, but roles can't be
   versioned or reused on their own.
2. **One repository per role**, the classic Galaxy style. Maximum separation, but many small repos to maintain, and
   shared logic (e.g. the generic Quadlet role) is awkward across repos.
3. **One Ansible collection in its own repository.** A single versioned package that holds all roles (and plugins,
   if needed later). This is the format current Ansible tooling is built around.

## Decision

Roles live in the collection **`cube_m4st3r.homelab`**, in its own repository. Working name:
`ansible-collection-homelab`, final name still open.

- The Galaxy namespace is `cube_m4st3r`: Galaxy names allow only lowercase letters, digits and underscores, so the
  hyphen in the GitHub username becomes an underscore.
- Core roles: `podman_host` (host preparation), `quadlet_app` (generic: vars in, Quadlet files out), plus one role
  per app that builds on `quadlet_app`.
- The collection is versioned with SemVer, released via release-please, and published to Ansible Galaxy.
- The `homelab` repo keeps only inventory, playbooks and docs. It pins a collection release in `requirements.yml`, and
  Renovate proposes updates.
- Playbooks run inside the execution environment image `homelab-ee`, which contains the collection.

## Consequences

- A change that needs both a new role feature and new inventory takes two pull requests, plus a collection release
  in between.
- The collection needs its own quality gates: ansible-lint, argument specs per role, Molecule tests, a release
  workflow.
- Role variables become a public interface. Renaming one is a breaking change and needs a major version, see the
  repo's semver skill.
- Other people can install the roles with `ansible-galaxy collection install cube_m4st3r.homelab`.
- `homelab` stays small and readable, which is what visitors look at first.
