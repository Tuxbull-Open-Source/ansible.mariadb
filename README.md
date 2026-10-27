# Ansible MariaDB role

Install and configure MariaDB, manage databases and users, and optionally
schedule local logical backups on Enterprise Linux 8 and 9. The role uses the
upstream MariaDB repository configured from `mariadb_version`.

## Requirements

- Ansible 2.11 or later.
- Enterprise Linux 8 or 9 target with network access to the selected MariaDB
  package repository.
- Controller collection dependency:

  ```bash
  ansible-galaxy collection install -r collections/requirements.yml
  ```

- A root password supplied through Ansible Vault or another approved secret
  manager. Do not put credentials in inventory, playbooks, or version control.

## Role variables

All defaults are in [`defaults/main.yml`](defaults/main.yml). Important values:

| Variable | Default | Purpose |
| --- | --- | --- |
| `mariadb_version` | `10.11` | MariaDB repository version. |
| `mariadb_secure_installation` | `true` | Enable secure-initialization tasks. |
| `mariadb_root_password` | empty | Required root password, supplied securely. |
| `mariadb_databases` | `[]` | Databases to create or remove. |
| `mariadb_users_create` | `[]` | Database users to create or update. Passwords are secrets. |
| `mariadb_users_remove` | `[]` | Database users to remove. This is destructive. |
| `mariadb_manage_only` | `false` | Skip repository and package installation when managing an existing server. |
| `mariadb_update_only` | `false` | Restrict execution to the role's update path. |
| `mariadb_backup_enable` | `true` | Enable the local logical-backup cron job. |
| `mariadb_backup_dir` | `/srv/bkp/mariadb` | Local backup destination. |

The default tuning values are examples, not capacity planning. Review and size
buffer, cache, and file settings for the target workload before deployment.

## Example playbook

Keep real values in an encrypted variable file, not this repository:

```yaml
---
- name: Install and configure MariaDB
  hosts: database
  become: true
  vars_files:
    - vault.yml
  roles:
    - role: tuxbull.mariadb
      vars:
        mariadb_root_password: "{{ vault_mariadb_root_password }}"
        mariadb_databases:
          - name: application
            encoding: utf8
            collation: utf8_general_ci
        mariadb_users_create:
          - name: application
            password: "{{ vault_mariadb_application_password }}"
            hosts:
              - localhost
            privs:
              - "application.*:ALL"
```

Install the role from Galaxy after publication:

```bash
ansible-galaxy role install tuxbull.mariadb
```

For development from this repository, use a local role path named `mariadb`.

## Operations and limitations

The role manages the MariaDB service, `/etc/my.cnf`, root client access,
databases, users, and a root-owned backup script plus cron entry. Confirm a
backup completed and is restorable before relying on it. The role does not
provide remote backup storage, backup restoration, automated secret rotation,
replication configuration, or an uninstall workflow.

Removing a database or user affects applications immediately. Back up the
affected data, plan downtime where needed, and validate connected applications
after every destructive change.

## Validation

Run YAML parsing, Ansible syntax validation, `git diff --check`, and the
approved live-test workflow described in [`AGENTS.md`](AGENTS.md). Syntax
checks alone do not prove package installation, database connectivity, backup
restorability, or idempotency.

## License

[LGPL-3.0](https://opensource.org/licenses/LGPL-3.0)

Copyright (c) Chris Ruettimann
