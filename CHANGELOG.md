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
