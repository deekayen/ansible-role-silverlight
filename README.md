Silverlight
===========

Install (or uninstall) Microsoft Silverlight on Windows.

Because the uninstall task uses Ansible's `raw` module, the play output will always report `ok` status instead of `changed`. The task does not wait for the msiexec process to exit before returning the `ok` status.

Requirements
------------

The target Windows machines must have whitelisted Internet access to download the Silverlight installer from [download.microsoft.com]().

Role Variables
--------------

By default, this role installs Microsoft Silverlight. Toggling the `silverlight_uninstall` variable from `false` to `true` will uninstall the framework if it exists.

To reboot Windows after successful installation, toggle the `silverlight_reboot` variable to `true`.

Dependencies
------------

None.

Example Playbook
----------------

    - hosts: servers
      roles:
         - { role: deekayen.silverlight, silverlight_uninstall: false, silverlight_reboot: false }

License
-------

BSD
