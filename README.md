user
====

Configure local system and normal users.

This role configures the following.

* Creates user and user group.
* Disable or removes a user.
* Set user SSH `authorized_keys`.
  * Where user access from.
  * What abilities can be performed.
* Optional set UID and GID.

Version
-------

* `master` --- latest development version

Requirements
------------

This role is limited to:

* Ubuntu 12.04
* Ubuntu 14.04
* Ubuntu 16.04
* Ubuntu 18.04
* CentOS 6
* CentOS 7
* CentOS 8

Role Variables
--------------

* `user_default_allow_ips` --- list of source ip addresses, default `[]`
* `user_default_restrict` --- comma separated string with restrictions for login, default `restrict,pty`. Available restrictions
    * `agent-forwarding` --- allow forward SSH agent
    * `port-forwarding` --- allow port forwarding
    * `pty` --- allow tty
    * `restrict` --- limit all, always use first
    * `X11-forwarding` --- allow X forwarding
    * See other options with `man sshd`
* `user_users` --- list of dicts with all root users, default `[]`. Dict list elements is defined as following
    * `allow_ips` --- list of source ip addresses, default `user_default_allow_ips`
    * `comment` --- user GECOS, default `""`
    * `create_home` --- create home directory, default `true`
    * `enabled` --- is the user enabled or not, default `false`
    * `enable_sudo_password` --- enable password check on sudo - use with `sudo`, default `false`
    * `gid` --- GID value as integer, default autogenerate
    * `groups` --- list of extra groups for the user, default `[]`
    * `group` --- users main group, default username
    * `key` --- file with one ssh key on each line, default `''`
    * `password` --- set password hash for user - , default `""`
    * `remove` --- remove user files when `enabled == false`, default `false`
    * `restrict` --- subset of restrictions, default `user_default_restrict`
    * `shell` --- set user default shell, default `bash`
    * `sudo` --- enable password less sudo for user, default `false`
    * `system` --- is user a system user, default `false`
    * `uid` --- UID value as integer, default autogenerate
    * `user` --- linux username, __required__

Dependencies
------------

None

Example Playbook
----------------

Variables are kept in the `host_vars` or `group_vars` folder usually. Defining everything in playbook is not recommended. This is just an example.

    - hosts: servers
      vars:
      roles:
        - role: met-root
          met_root_allow_ips:
            - 10.0.0.0/8
            - 172.16.0.0/24
            - 192.168.1.2
          met_root_restrict: restrict,pty
          met_root_users:
            - user: ansible
              key: |
                ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHkCVF05JvfkrfOOESivOxV4N8+A/EMEkF7/nCQMRoQg
              enabled: true
            - user: aheimsbakk
              key: |
                ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCyLO8A5/7+Ckyl5REO5RpVg1Hpm4t9oM+Rum6k9W8fr6MqgpbJuDJ9mmb5AsoaL80J62mvZQZqXqYCtAfL7ZXMcZZiFwbhpdwRehFfR8vDjae4P4AooV7Vk99I1jvumes1z/FQMvue27lromgMejq/GcEAndlY9smYuQXLy3ajUw3dV+w4rm4L3+Qbps1lft8PZU/G0FMMqqTIPPdcLOLgD/d/625RhVtwqx0C/z3fykylFJ4RkWYQK5p0F/BO8Rr1un7AWYl2ITnOpxe99B9YfE2GI9sTvFAp221cEiozRPBIGJIa1JoZl+tDzb4XbF27Zjs3+6ywbSC5nrIW5gArlgavwKd+fL6vyDJXD+NM86QcGZvlhnMr4wanfHo6JVnekgbxWVaag/JY+yVO3RVZ/VdN+4nF1PXyjmdaLGebUrTDCh2JiPmSZ5Q7y9oyH5/ayJnGxVGCN/d8RK1So2UUhkWV+Z4J22yc2PEc1RD7HyLEI1t9WCT6nciY/t5it2E=
              allow_ips:
                - 0.0.0.0/0
              restrict: restrict,pty,agent-forwarding
              enabled: true
          met_root_users_limit:
            - aheimsbakk
          met_root_users_always:
            - ansible

### Add custom values in recipe

Append to default lists in `group_vars` or `host_vars`.

#### Append to list element

* In `group_vars` or `host_vars`.
    ```
    met_root_users_custom:
      - user: sverrest
        key: |
          ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHkCVF05JvfkrfOOESivOxV4a8+A/EMEkF7/nCQMRoQg
        enabled: true
    ```
* In playbook, merge defaults with your custom values.
    ```
    pre_tasks:
      - name: add custom met_root_users
        set_fact:
          met_root_users: '{{ met_root_users + met_root_users_custom|default([]) }}'
    ```

Testing
-------

Testing the role with Vagrant running on VirtualBox.

    cd tests
    vagrant up

Rerun tests.

    vagrant provision

Remove test VMs.

    vagrant destroy -f

License
-------

GPLv2+

Author Information
------------------

Arnulf Heimsbakk <aheimsbakk@met.no>

###### set vim: spell spelllang=en:
