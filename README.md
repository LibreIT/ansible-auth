[![Build Status](https://travis-ci.org/LibreIT/ansible-auth.png?branch=master)](https://travis-ci.org/LibreIT/ansible-auth)

ansible auth role
========

This role implements the following auth related functions: 

      - users
        - disable root login (highly recommended)
        - users in the "admins" group can become root and can have unix password
        - the "lifesaver" user can become an user of "admins" group (optional)

My use cases:

      - normal case (with my workstation)
        - login in my workstation with a two-factor authentication system (I use yubikey)
        - login in servers only with ssh key (without password) in encripted fs

      - without encripted partition (or with my smartphone)
        - ssh key with password

      - only with a ssh client
        - connect to "lifesaver" user with ssh and strong password
        - "lifesaver" can become one of the admin users using the user's unix password
           with command "sudo -i -u **user-admin**""

Role Variables
--------------

      auth_disable_root: false        # disable root login
      
      auth_sysadmin_users:            # add this users as administrators
       - name: kalos
         uid: 9871
         pass: $6$9AMxXEeqfersx78S$j4oq0TqTBNsjzoy3tXk4hf8h2hfckAZl0KVah6C/IYPqmuDmYUNDOOtfbTJfEo8LJVRlHR7xSi/gOcVQMYQ81
         shell: /bin/bash
         ssh_keys:
           - aaaa
           - bbbb
        - {name: calogero, uid: 5010, pass: PASSWORD, ssh_key: aaaa.. }
        - {name: lillo, uid: 5011, pass: PASSWORD }
      
      auth_sysadmin_group: admins     # administration group name
      auth_sysadmin_gid: 2000         # administration group GID
      
      auth_lifesaver: true            # create lifesaver user
      auth_lifesaver_user: lifesaver  # name of "lifesaver" user
      auth_lifesaver_pass: PASSWORD
      auth_lifesaver_uid: 3000
      auth_lifesaver_shell: /bin/rbash

      # use willshersystems.sshd role to configure ssh
      sshd_skip_defaults: true
      sshd_PasswordAuthentication: no
      sshd_match:
        - Condition: "User lifesaver"
          PasswordAuthentication yes

Examples
========

1) Simple use, with all features.

    - hosts: example
      roles:
         - { role: libreit.auth }

2) Create users with "admins" group.

    - hosts: example

      roles:
        - { role: libreit.auth, auth_sysadmin_users:
                              [{name: kalos, pass: pass1, uid: 1010},
                               {name: elisa, pass: pass2, uid: 1011}],
                              auth_sysadmin_group: admins,
                              auth_sysadmin_gid: 1234 }

Exec this playbook with tags:

    ansible-playbook playbook.yml --tags users


3) Create "lifesaver" user.

    - hosts: example

      roles:
        - { role: libreit.auth, auth_lifesaver: true,
                              auth_lifesaver_user: very_special_user,
                              auth_lifesaver_pass: very_strong_pass,
                              auth_lifesaver_uid: 3000,
                              auth_sysadmin_group: admins}

Exec this playbook with tags:

    ansible-playbook playbook.yml --tags lifesaver


License
-------

GPLv3

Author Information
------------------

Calogero Lo Leggio (kalos) - [LibreIT](http://LibreIT.org)
