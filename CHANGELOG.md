# Changelog

## v1.0.1 - 2026-10-27

### Fixed

- Discover role tasks through Ansible's controller-side `role_path`, allowing a
  role installed with `ANSIBLE_ROLES_PATH` or from Galaxy to execute its
  OS-aware dispatcher.
- Avoid repeated root-password changes when the managed root client
  configuration already authenticates successfully.
- Mark secure-initialization SQL as converged after it has applied its
  idempotent secure state.

### Verified

- Live install and configuration on a disposable Rocky Linux 9.8 target.
- MariaDB service, database/user reconciliation, logical backup output, and
  scheduled backup entry.
- Root-client configuration recovery, the secure-installation toggle, and a
  final same-input convergence with no changes.

## v1.0.0 - 2026-10-27

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
