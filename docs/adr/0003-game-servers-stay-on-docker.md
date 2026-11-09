# 0003. Game servers stay on Docker for now

- Status: accepted
- Date: 2026-11-09

## Context

[0002](0002-podman-quadlets-instead-of-docker-compose.md) moves services to Podman Quadlets. The game servers are
different:

- They are managed by [AMP](https://cubecoders.com/AMP), which creates and controls its instances through Docker.
- The Minecraft setup sits behind a Velocity proxy and is reachable from the internet via playit.gg, because the
  connection is behind CGNAT.
- The setup works today. Moving it means testing AMP against Podman's Docker-compatible API, which is not a supported
  or documented path.

Migrating everything at once would put a working setup at risk for no immediate gain.

## Options considered

1. **Migrate the game servers to Podman together with everything else.** Consistent, but AMP compatibility is unknown
   and debugging it would block the rest of the migration.
2. **Replace AMP with plain Quadlets per game server.** Fully consistent, but loses AMP's management features and
   turns a working setup into a project of its own.
3. **Keep them on Docker for now** and revisit once the rest is migrated.

## Decision

The game servers **stay on Docker, managed by AMP**, outside the Quadlet setup:

- The Ansible collection does not manage the game server containers. At most it manages the host basics (users,
  SSH, packages).
- Exposure stays as is: Velocity proxy + playit.gg.
- This is revisited after the other services run as Quadlets (roadmap phase 5).

## Consequences

- Two container runtimes exist in the homelab: Podman for services, Docker for game servers. Documentation and
  monitoring have to cover both.
- The game server setup is not reproducible from the repos yet.
- The migration of everything else is not blocked by an AMP question.
- A future ADR will supersede this one, either moving the game servers to Podman (with or without AMP) or making
  Docker for game servers a permanent choice.
