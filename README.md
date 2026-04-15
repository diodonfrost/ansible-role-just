ansible-role-just
=============

[![molecule](https://github.com/diodonfrost/ansible-role-just/workflows/molecule/badge.svg)](https://github.com/diodonfrost/ansible-role-just/actions)
[![Ansible Galaxy](https://img.shields.io/badge/galaxy-diodonfrost.just-660198.svg)](https://galaxy.ansible.com/diodonfrost/just)

Ansible role that installs [`just`](https://github.com/casey/just), the handy command runner, on Linux, macOS, FreeBSD, OpenBSD, and Windows.

Role Variables
--------------

All variables are defined in `defaults/main.yml` and may be overridden at play or inventory scope.

| Variable | Default | Description |
| --- | --- | --- |
| `just_install_method` | `package` | How to install `just`. Set to `package` to use the OS package manager, or `binary` to download the release archive from GitHub (Linux/macOS only). |
| `just_version` | `latest` | Version to install when `just_install_method: binary`. `latest` queries the GitHub releases API; otherwise provide a tag such as `1.36.0`. Ignored for `package` installs. |
| `just_package_name` | OS-specific (`just`, or `Casey.Just` on Windows) | Package name passed to the target's package manager. |
| `just_pkg_url` | OS-specific | Full URL of the tarball to download when `just_install_method: binary`. Templated from `just_version` and the host's architecture; override only to mirror releases or pin to a custom build. |
| `just_path` | `/usr/local/bin/` on Linux/macOS | Directory where the `just` binary is extracted when `just_install_method: binary`. |

Architecture detection for binary installs uses a map (`_just_arch` in `vars/main.yml`) from Ansible's `architecture` fact to the arch segment used in the GitHub release asset names. `x86_64`, `amd64`, `aarch64`, `armv7l`, `arm`, and `i386` are supported out of the box; unknown values fall back to `x86_64`.

Dependencies
------------

None. The role has no hard dependencies on other Galaxy roles or collections. The Molecule test scenarios use `diodonfrost.update_package_manager_cache` and `ansible.windows` to prepare test hosts, but those are not required when consuming the role.

Example Playbook
----------------

Install `just` from the OS package manager (default):

```yaml
- hosts: servers
  roles:
    - role: diodonfrost.just
```

Install a specific version from upstream release tarballs (Linux/macOS):

```yaml
- hosts: servers
  roles:
    - role: diodonfrost.just
      vars:
        just_install_method: binary
        just_version: 1.36.0
        just_path: /usr/local/bin/
```

Install the latest release to a user-writable directory:

```yaml
- hosts: servers
  roles:
    - role: diodonfrost.just
      vars:
        just_install_method: binary
        just_version: latest
        just_path: "{{ ansible_env.HOME }}/.local/bin/"
```

Local Testing
-------------

This project uses [Molecule](http://molecule.readthedocs.io/) to aid in the
development and testing.

To develop or test you'll need to have installed the following:

* Linux (e.g. [Ubuntu](http://www.ubuntu.com/))
* [Docker](https://www.docker.com/)
* [Python](https://www.python.org/) (including python-pip)
* [Ansible](https://www.ansible.com/)
* [Molecule](http://molecule.readthedocs.io/)
* [Libvirt](https://libvirt.org/) (If you test a system other than Linux)
* [Vagrant](https://www.vagrantup.com/downloads.html) (If you test a system other than Linux)

Testing with Docker
-------------------

```shell
# Install requirements
pip install -r requirements-dev.txt

# Test ansible role with ubuntu 26.04
molecule test

# Test ansible role with debian 13
image=ansible-debian:13 molecule test

# Test ansible role with alpine latest
image=ansible-alpine:latest molecule test

# Create debian 13 instance
image=ansible-debian:13 molecule create

# Apply role on debian 13 instance
image=ansible-debian:13 molecule converge

# Launch tests on debian 13 instance
image=ansible-debian:13 molecule verify
```

Testing with Vagrant and Libvirt
--------------------------------

```shell
# Test ansible role with Linux
molecule test -s linux

# Test ansible role with Windows
molecule test -s windows
```

License
-------

Apache Software License 2.0

Author Information
------------------

This role was created in 2023 by diodonfrost.
