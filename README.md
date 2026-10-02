# deekayen.silverlight

[![CI](https://github.com/deekayen/ansible-role-silverlight/actions/workflows/ci.yml/badge.svg)](https://github.com/deekayen/ansible-role-silverlight/actions/workflows/ci.yml) [![Ansible Galaxy](https://img.shields.io/badge/galaxy-deekayen.silverlight-blue.svg)](https://galaxy.ansible.com/ui/standalone/roles/deekayen/silverlight/) [![Project Status: Unsupported – The project has reached a stable, usable state but the author(s) have ceased all work on it. A new maintainer may be desired.](https://www.repostatus.org/badges/latest/unsupported.svg)](https://www.repostatus.org/#unsupported) ![BSD 3-Clause license](https://img.shields.io/badge/license-BSD%203--Clause-blue)

> **Deprecated.** Microsoft ended Silverlight support in October 2021. The role
> is kept for existing hosts. CI lints and syntax-checks it; it has no Windows
> test host.

An Ansible role that installs Microsoft Silverlight on a Windows host, or uninstalls it when `silverlight_uninstall` is `true`.

The install task picks the 64-bit or 32-bit `download.microsoft.com` installer URL from `vars/main.yml` based on `ansible_facts.architecture`, then runs it with `/q` through `ansible.windows.win_package`, using product ID `{89F4137D-6C26-4A84-BDB8-2E5A4BB71E00}` as the installed-state check. The uninstall task is a `raw` PowerShell command that runs `msiexec.exe /x` against the same product ID with `/qn` and waits for it to exit. See [Known issues](#known-issues) before using the install path.

## Requirements

- ansible-core 2.15 or newer on the controller.
- The `ansible.windows` collection: `ansible-galaxy collection install ansible.windows`.
- A WinRM or SSH connection to the target with administrative rights.
- Fact gathering left on for installs. The role reads `ansible_facts.architecture` to choose the installer.

## Supported platforms

| Platform | Versions |
| --- | --- |
| Windows | 2012R2 |

CI lints the role and runs `ansible-playbook --syntax-check`; it does not apply the role to a Windows host.

## Installation

From Ansible Galaxy:

```bash
ansible-galaxy role install deekayen.silverlight
ansible-galaxy collection install ansible.windows
```

Or pin it in `requirements.yml`:

```yaml
---
roles:
  - name: deekayen.silverlight
    src: https://github.com/deekayen/ansible-role-silverlight.git
    scm: git
    version: main

collections:
  - name: ansible.windows
```

```bash
ansible-galaxy install -r requirements.yml
```

## Role variables

`meta/argument_specs.yml` validates both as booleans.

| Variable | Default | Description |
| --- | --- | --- |
| `silverlight_uninstall` | `false` | `false` installs Silverlight; `true` uninstalls it. |
| `silverlight_reboot` | `false` | Intended to reboot after an install that requests one. See [Known issues](#known-issues). |

The installer URLs are the internal `silverlight_url32` and `silverlight_url64` values in `vars/main.yml`.

## Behavior

- Every uninstall run reports `changed`, since the `raw` task sets `changed_when: true`. The task does not check the `msiexec` exit code or whether the product is still installed afterward.

## Dependencies

None. The `ansible.windows` collection is a requirement, not a role dependency.

## Example playbook

Remove Silverlight from hosts that still have it:

```yaml
---
- name: Uninstall Microsoft Silverlight.
  hosts: legacy_windows_desktops

  vars:
    silverlight_uninstall: true

  roles:
    - deekayen.silverlight
```

## Known issues

- Both installer URLs in `vars/main.yml:3` and `vars/main.yml:4` return HTTP 404 as of October 2026, so the install task fails on any host that does not already have Silverlight.
- The reboot task in `tasks/main.yml:25` checks `silverlight_exe.restart_required`. `ansible.windows.win_package` returns `reboot_required`, not `restart_required`, so `silverlight_reboot: true` never reboots the host.

## Development

CI runs on every push to `main` and every pull request (see `.github/workflows/ci.yml`). It installs `ansible.windows`, runs `ansible-lint --profile production`, and syntax-checks `tests/test.yml`. To run the same checks locally:

```bash
pip3 install ansible-lint
ansible-galaxy collection install ansible.windows
ansible-lint --profile production
mkdir -p .ansible/roles && ln -sfn "$PWD" .ansible/roles/deekayen.silverlight
ANSIBLE_ROLES_PATH=.ansible/roles:~/.ansible/roles ansible-playbook --syntax-check tests/test.yml -i tests/inventory
```

The repository also has a `.pre-commit-config.yaml`; run `pre-commit run --all-files` before pushing.

### Repository layout

| Path | Purpose |
| --- | --- |
| `tasks/main.yml` | Install block and uninstall block, selected by `silverlight_uninstall`. |
| `vars/main.yml` | 32-bit and 64-bit installer URLs. |
| `defaults/main.yml` | Every user-facing variable. |
| `meta/argument_specs.yml` | Type validation for the variables. |
| `tests/` | Syntax-check playbook and inventory used by CI. |

## Releases

Pushing a git tag runs `.github/workflows/release.yml`, which imports the tagged commit into Ansible Galaxy as `deekayen.silverlight`. The import needs a `GALAXY_API_KEY` repository or organization secret.

## License

BSD 3-Clause. See [LICENSE](LICENSE).

## Author

[David Norman](https://github.com/deekayen). Sponsorship links are in [.github/FUNDING.yml](.github/FUNDING.yml).
