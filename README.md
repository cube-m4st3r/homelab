# homelab

[![ci](https://github.com/cube-m4st3r/homelab/actions/workflows/ci.yml/badge.svg)](https://github.com/cube-m4st3r/homelab/actions/workflows/ci.yml)
[![pre-commit](https://img.shields.io/badge/pre--commit-enabled-brightgreen?logo=pre-commit)](https://pre-commit.com/)

My self-hosted homelab, as code. Ansible deploys rootless **Podman Quadlets** onto a handful of hosts. Everything runs behind
**Cloudflare Tunnel**, with single sign-on through **Authentik**.

It's public on purpose: take whatever is useful to you. Every bigger choice is explained in an
[Architecture Decision Record](docs/adr/), so you can see the *why*, not just the YAML.

> **Status:** work in progress. Services are moving from Docker Compose to Podman Quadlets one at a time.

## What runs here

| Service | Purpose | Exposed via | Runtime |
| --- | --- | --- | --- |
| [Authentik](https://goauthentik.io/) | SSO / identity provider (OIDC, LDAP) | Cloudflare Tunnel + Access | Docker → Podman |
| [cloudflared](https://github.com/cloudflare/cloudflared) | Outbound tunnel, no open ports at home | n/a | Docker → Podman |
| [Stalwart](https://stalw.art/) | Mail server | Cloudflare Tunnel | Docker → Podman |
| Game servers ([AMP](https://cubecoders.com/AMP)) | Minecraft behind a Velocity proxy | [playit.gg](https://playit.gg/) | Docker (for now) |

<!-- TODO: add the rest of your services -->

**Why a tunnel?** The connection sits behind CGNAT, so there's no public IPv4 to forward ports to. Web services go out through
Cloudflare Tunnel, game traffic through playit.gg.

## How it fits together

```text
ansible-collection-homelab  ── roles (how to deploy) ──┐
                                                       ▼
homelab (this repo)  ── inventory + playbooks (what runs where) ──►  hosts  ◄── images from ghcr.io/cube-m4st3r/*
```

- **[ansible-collection-homelab](https://github.com/cube-m4st3r/ansible-collection-homelab)** holds the reusable roles:
  `podman_host`, `quadlet_app` and one role per app.
- **This repo** holds only the inventory, the playbooks and the docs. Host vars *describe* the apps, and the roles turn
  them into Quadlet files.
- **[homelab-ee](https://github.com/cube-m4st3r/homelab-ee)** is the execution environment: every playbook runs inside
  this image, locally and in CI.
- **Updates are pulled, never pushed:** hosts fetch config via `ansible-pull` and images via `podman auto-update`.
  Nothing on the internet can reach into the network.

## Repository layout

```text
.
├── ansible.cfg
├── ansible-navigator.yml     # runs playbooks inside the EE image
├── requirements.yml          # pins cube_m4st3r.homelab
├── inventories/
│   └── prod/
│       ├── hosts.yml
│       ├── group_vars/
│       └── host_vars/        # which apps run on which host
├── playbooks/
│   ├── site.yml
│   ├── base.yml
│   └── apps/
└── docs/
    ├── architecture.md
    └── adr/                  # architecture decision records
```

## Usage

Requirements on your machine: [Podman](https://podman.io/), [ansible-navigator](https://ansible.readthedocs.io/projects/navigator/)
and [pre-commit](https://pre-commit.com/). Everything else lives in the EE image.

```bash
git clone https://github.com/cube-m4st3r/homelab.git && cd homelab
pre-commit install

# dry run, then for real
ansible-navigator run playbooks/site.yml -i inventories/prod --check --diff
ansible-navigator run playbooks/site.yml -i inventories/prod
```

Host requirements: Podman **4.4 or newer** (Quadlet support) and systemd.

## Secrets

No secret is ever committed. [gitleaks](https://github.com/gitleaks/gitleaks) checks every commit locally (pre-commit)
and the full history in CI. Encrypted secrets (SOPS + age) will follow, see the ADRs.

## Contributing

This is a personal setup, but issues and ideas are welcome. Commits follow
[Conventional Commits](https://www.conventionalcommits.org/), and pre-commit enforces it.

## Related repositories

| Repo | What it is |
| --- | --- |
| [ansible-collection-homelab](https://github.com/cube-m4st3r/ansible-collection-homelab) | Ansible roles for Podman Quadlets |
| [homelab-ee](https://github.com/cube-m4st3r/homelab-ee) | Ansible execution environment image |
| [homelab-toolbox](https://github.com/cube-m4st3r/homelab-toolbox) | Dev container with all the tooling |
| [.github](https://github.com/cube-m4st3r/.github) | Shared CI workflows |

## License

[MIT](LICENSE)
