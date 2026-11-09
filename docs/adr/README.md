# Architecture Decision Records

Why this homelab looks the way it does. Each record captures one decision: the context, the options considered, what
was decided, and what that costs. See [0001](0001-record-architecture-decisions.md) for how this log works, and use
[`template.md`](template.md) for new records.

| # | Decision | Status |
| --- | --- | --- |
| [0001](0001-record-architecture-decisions.md) | Record architecture decisions | accepted |
| [0002](0002-podman-quadlets-instead-of-docker-compose.md) | Use Podman Quadlets instead of Docker Compose | accepted |
| [0003](0003-game-servers-stay-on-docker.md) | Game servers stay on Docker for now | accepted |
| [0004](0004-ansible-in-a-separate-collection-repo.md) | Ansible roles live in a separate collection repository | accepted |
| [0005](0005-one-repository-per-container-image.md) | One repository per container image | accepted |

Coming up: 0006 (deployment model: pull-based vs. self-hosted runner) and 0007 (secret management).
