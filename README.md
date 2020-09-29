user
====

Configure system and normal groups and users. This role configures the following. Groups are created before users.

* Creates or removes groups.
* Creates user and user group.
* Disable or removes a user.
* Set user SSH `authorized_keys`.
  * Where user access from.
  * What abilities can be performed.
* Optional set UID and GID.

Version
-------

* `1.3.0` --- remove ubuntu precise from testing
* `1.2.0` --- updated with ubuntu focal
* `1.1.6` --- tested with Ansible 2.9.11
* `1.1.5` --- prepare for github
* `1.1.4` --- do not try to remove sudoers file in `check_mode`
* `1.1.3` --- fixed unnecessary password updates
* `1.1.2` --- default to hard coded `/bin/bash` if `shell` is undefined
* `1.1.1` --- remove unused issue tracker from meta
* `1.1.0` --- set `user_default_restrict` to an empty string
* `1.0.0` --- initial version
* `master` --- latest development version

Requirements
------------

This role is limited to:

* Ubuntu 14.04
* Ubuntu 16.04
* Ubuntu 18.04
* Ubuntu 20.04
* CentOS 6
* CentOS 7
* CentOS 8

Role Variables
--------------

* `user_default_allow_ips` --- list of source ip addresses, default `[]`
* `user_default_restrict` --- comma separated string with restrictions for login, default `""`. Available restrictions
    * `agent-forwarding` --- allow forward SSH agent
    * `port-forwarding` --- allow port forwarding
    * `pty` --- allow tty
    * `restrict` --- limit all, always use first
    * `X11-forwarding` --- allow X forwarding
    * See other options with `man sshd`
* `user_groups` --- list of dicts with all groups, default `[]`. Dict elements is defined as following
    * `enabled` --- is the group enabled or not, default `false`
    * `gid` --- GID value as integer, default auto generate
    * `group` --- Linux group name, __required__
    * `system` --- is the group a system grou, default `false`
* `user_users` --- list of dicts with all users, default `[]`. Dict list elements is defined as following
    * `allow_ips` --- list of source ip addresses, default `user_default_allow_ips`
    * `comment` --- user GECOS, default `""`
    * `create_home` --- create home directory, default `true`
    * `enabled` --- is the user enabled or not, default `false`
    * `enable_sudo_password` --- enable password check on sudo - use with `sudo`, default `false`
    * `gid` --- GID value as integer, default auto generate
    * `groups` --- list of extra groups for the user, default `[]`
    * `group` --- users main group, default username
    * `key` --- file with one ssh key on each line, default `''`
    * `password` --- set password hash for user - create new password with `mkpasswd -m sha512crypt`, default not set
    * `remove` --- remove user files when `enabled == false`, default `false`
    * `restrict` --- subset of restrictions, default `user_default_restrict`
    * `shell` --- set user default shell, default `bash`
    * `sudo` --- enable password less sudo for user, default `false`
    * `system` --- is user a system user, default `false`
    * `uid` --- UID value as integer, default auto generate
    * `user` --- Linux user name, __required__

Dependencies
------------

None

Example Playbook
----------------

Variables are kept in the `host_vars` or `group_vars` folder usually. Defining everything in playbook is not recommended. This is just an example.

    - hosts: servers
      vars:
      roles: user
      user_default_allow_ips:
        - 0.0.0.0/0
      user_default_restrict: restrict,pty
      user_users:
        - user: sysuser1
          create_home: false
          enabled: true
          system: true
        - user: sysuser2
          create_home: true
          enabled: true
          system: true
        - user: user1
          comment: User Name,Building and room or contact person,Office Phone,Home Phone,Email
          enabled: true
        - user: user1
          enabled: false
          remove: true
        - user: user2
          comment: User Name,Building and room or contact person,Office Phone,Home Phone,Email
          enabled: true
          group: usergroup2
          groups: adm,users
          gid: 2000
          uid: 2000
          allow_ips:
            - 10.0.0.0/8
            - 192.168.1.0/24
          key: |
            ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHkCVF05JvfkrfOOESivOxV4N8+A/EMEkF7/nCQMRoQg
            ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINy/2ldIkVhcgUAF3XkMyjfhXxuMHn0yK/1vqJwXFiue
          shell: /bin/sh
          restrict: restrict,X11-forwarding,pty
          # password 'user2'
          password: $6$W7eiMn9W7lWln0ea$H7Ys/saS9vPt4ng.dKQeExzbR8tFTIOn/MZ.C7HmVCsL..5SDHgnX4lvAE6JjQjCou2fcUPgkwQ1qInySeoMp.
          sudo: true
          enable_sudo_password: true
        - user: user3
          enabled: true
          key: |
            ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIMFqjOZQuNPahtMmEWocMpW1oN7sukT+PmcWSqFdmSaj
          sudo: true

### Add custom values in recipe

Append to default lists in `group_vars` or `host_vars`.

#### Append to list element

* In `group_vars` or `host_vars`.
    ```
    user_users_custom:
      - user: user4
        key: |
          ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINWIFe749/NkJcpEW1H8DtUJrnbNdvexBPiEeXyp6/uY
        enabled: true
    ```
* In playbook, merge defaults with your custom values.
    ```
    pre_tasks:
      - name: add custom user_users
        set_fact:
          user_users: '{{ user_users + user_users_custom|default([]) }}'
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

GPLv2

Author Information
------------------

Arnulf Heimsbakk <aheimsbakk@met.no>

###### set vim: spell spelllang=en:
