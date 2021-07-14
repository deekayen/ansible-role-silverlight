Silverlight
===========
[![Project Status: Unsupported – The project has reached a stable, usable state but the author(s) have ceased all work on it. A new maintainer may be desired.](https://www.repostatus.org/badges/latest/unsupported.svg)](https://www.repostatus.org/#unsupported)

Install (or uninstall) Microsoft Silverlight on Windows using Ansible.

Because the uninstall task uses Ansible's `raw` module, the play output will always report `ok` status instead of `changed`. The task does not wait for the msiexec process to exit before returning the `ok` status and does not know if uninstallation fails.

Requirements
------------

The target Windows machines must have whitelisted Internet access to download the Silverlight installer from [download.microsoft.com]().

The control machine should have `pywinrm` and one of `requests-ntlm`, `requests-kerberos`, and/or `requests-credssp` installed by `pip`.

Role Variables
--------------

By default, this role installs Microsoft Silverlight. Toggling the `silverlight_uninstall` variable from `false` to `true` will uninstall the framework if it exists.

To reboot Windows after successful installation, toggle the `silverlight_reboot` variable to `true`.

Dependencies
------------

None.

Example Playbook
----------------

    ---

    - name: Install Microsoft Silverlight.
      hosts: servers

      roles:
         - role: deekayen.silverlight
           silverlight_uninstall: false
           silverlight_reboot: false

License
-------

BSD
