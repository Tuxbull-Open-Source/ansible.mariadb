# Changelog

## Unreleased

### Security

- Remove the shipped usable MariaDB root-password default and require an
  operator-supplied secret.
- Validate required secret input before running installation or configuration
  tasks and suppress the validation output.

### Changed

- Align Galaxy metadata with `tuxbull.mariadb` and the
  `Tuxbull-Open-Source/ansible.mariadb` source repository.
- Rewrite role usage documentation and add maintainer instructions for
  validation, release, and Galaxy import gates.
- Correct the test role basename for local syntax validation.
- Make `mariadb_manage_only` skip MariaDB repository and package changes.
- Honor `mariadb_secure_installation: false` by skipping secure-initialization
  tasks.

### Validation

- Parse all role YAML and pass the local Ansible syntax check through a
  disposable harness. No live MariaDB deployment, restore, or idempotency test
  is claimed for this release preparation.
