# opensource-mirrors

Automated mirroring of open-source repositories used by
[ansible-infrastructure](https://github.com/codefuturist/ansible-infrastructure)
to GitLab (`gitlab.com/Koala101/opensource-mirrors`).

## GitLab Structure

```
Koala101/
└── opensource-mirrors/           ← top-level group
    ├── ansible-roles/            ← subgroup (19 repos)
    │   ├── ansible-role-wireguard
    │   ├── ansible-role-docker
    │   ├── k3s-ansible
    │   └── ...
    └── ansible-collections/      ← subgroup (31 repos)
        ├── community.docker
        ├── community.general
        ├── amazon.aws
        └── ...
```

## What gets mirrored

| Category | Count | Examples |
|----------|-------|---------|
| Git submodules (→ `ansible-roles/`) | 19 | geerlingguy roles, k3s-ansible, wireguard, proxmox |
| Ansible collections (→ `ansible-collections/`) | 31 | community.docker, community.general, amazon.aws |

**Total: 50 repositories** — all branches, tags, and full history.

## How it works

1. **`setup-gitlab`** — creates the top-level group and subgroups via GitLab API (idempotent)
2. **`prepare`** — reads `mirrors.json` and builds the job matrix
3. **`mirror`** (×50, 5 parallel) — for each repo: create GitLab project if missing → `git clone --bare` → `git push --mirror`
4. Runs **daily at 03:00 UTC** via cron, or on-demand via workflow dispatch

## Manual trigger

```bash
# Mirror all repos
gh workflow run mirror.yml

# Mirror only repos matching a filter
gh workflow run mirror.yml -f repo_filter=geerlingguy
```

## Configuration

| File | Purpose |
|------|---------|
| `mirrors.json` | Source repos, target subgroups |
| `GITLAB_TOKEN` secret | GitLab PAT with `api` + `write_repository` scopes |

## Adding a new repo

Edit `mirrors.json` and add an entry:

```json
{
  "source": "owner/repo-name",
  "subgroup": "ansible-roles"
}
```

## GitLab project settings (auto-applied)

Mirrored projects are created with issues, MRs, wiki, and CI disabled — they are read-only mirrors.
