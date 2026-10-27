# Repository instructions

## Scope

This repository contains the reusable `tuxbull.mariadb` Ansible role. Keep it
focused on MariaDB installation, configuration, secure initial setup, database
and user reconciliation, and optional local backups for supported systems. Do
not commit credentials, private keys, database dumps, inventories, lab logs,
provider state, migration notes, or internal infrastructure details.

## Supported platform and dependencies

The maintained platform matrix is Enterprise Linux 8 and 9. Controller-side
Ansible collections are declared in `collections/requirements.yml`:

```bash
ansible-galaxy collection install -r collections/requirements.yml
```

The role has no Ansible role dependencies. Do not modify downloaded collections
in place.

## Configuration and security

`mariadb_root_password` is required when installing or configuring a server.
Provide it through Ansible Vault or another approved secret manager. Never add
a usable default password. User passwords in `mariadb_users_create` are also
secrets and must not be committed or printed. Tasks handling credentials must
use `no_log: true`.

Database user removal is destructive. The role does not delete databases unless
an entry in `mariadb_databases` explicitly requests `state: absent`. Back up
the affected database and verify consumer impact before running destructive
changes on a managed target.

## Validation

Run from the repository root:

```bash
python3 - <<'PY'
from pathlib import Path
import yaml
files = list(Path('.').rglob('*.yml')) + list(Path('.').rglob('*.yaml'))
for path in files:
    yaml.safe_load(path.read_text())
print(f'parsed {len(files)} YAML files')
PY
HARNESS_DIR="$(mktemp -d)"
trap 'rm -rf "$HARNESS_DIR"' EXIT
mkdir -p "$HARNESS_DIR/roles"
cp -a . "$HARNESS_DIR/roles/mariadb"
ANSIBLE_ROLES_PATH="$HARNESS_DIR/roles" \
  ansible-playbook -i 'localhost,' -c local tests/playbook_mariadb_full.yml --syntax-check
git diff --check
```

Syntax validation does not install or configure MariaDB. Live testing needs an
explicitly approved disposable EL 8 or 9 target, a temporary non-secret
inventory outside this repository, functional checks for service state,
database/user reconciliation and backup behavior, an idempotency run, and
cleanup of all temporary resources.

## Documentation and publication

`README.md` documents role usage and supported configuration. `CHANGELOG.md`
contains change and release history. Keep releases and version history out of
the README.

Publication identities are separate:

- GitHub source: `Tuxbull-Open-Source/ansible.mariadb`
- Galaxy namespace: `tuxbull`
- Galaxy role: `tuxbull.mariadb`

Do not self-merge a pull request. After a maintainer merges a release PR,
verify the exact default-branch commit and changelog entry before creating an
immutable three-component SemVer tag and matching published GitHub Release.
Only then import the reviewed source into Galaxy. Poll the import task, verify
the role's source owner/repository and version record, and install both the
unpinned and version-pinned Galaxy FQN in fresh temporary directories before
reporting publication complete.
