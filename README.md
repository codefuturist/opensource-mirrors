# opensource-mirrors

Automated mirroring of open-source repositories used by
[ansible-infrastructure](https://github.com/codefuturist/ansible-infrastructure)
to GitLab (`gitlab.com/Koala101`).

## What gets mirrored

| Category | Count | Examples |
|----------|-------|---------|
| Git submodules | 19 | geerlingguy roles, k3s-ansible, wireguard, proxmox |
| Ansible collections | 31 | community.docker, community.general, amazon.aws, etc. |

**Total: 50 repositories**

## How it works

- A GitHub Actions workflow runs **daily at 03:00 UTC**
- For each repo in `mirrors.json`: `git clone --bare` → `git push --mirror` to GitLab
- GitLab projects are auto-created via API if they don't exist
- All branches, tags, and full history are mirrored
- Runs up to 5 mirrors in parallel

## Manual trigger

Run from the Actions tab or via CLI:

```bash
# Mirror all repos
gh workflow run mirror.yml

# Mirror only repos matching a filter
gh workflow run mirror.yml -f repo_filter=geerlingguy
```

## Configuration

- **`mirrors.json`** — list of source repos to mirror
- **`GITLAB_TOKEN`** — GitLab personal access token (stored as GitHub Actions secret)

## Adding a new repo

Edit `mirrors.json` and add an entry:

```json
{
  "source": "owner/repo-name",
  "category": "submodules"
}
```
