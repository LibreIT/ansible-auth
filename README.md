ansible auth role
========

This role implements the following authentication pattern:

- users
  - root account can be disabled in ssh and local login (highly recommended)
  - users in the "admins" group can become root and can have unix password
  - the "lifesaver" user can become an user of "admins" group (optional)

- auth
  - admin users use only ssh key (with or without password)
  - "lifesaver" use a very strong password
  - "lifesaver" can become one of the admin users using the user's unix password 

- sshd config
  - disable root login
  - login only with ssh keys (except for "lifesaver" user)
  - change sshd port (optional)  


My use cases:
- normal case (with my workstation)
  - login with a two-factor authentication system (I use yubikey)
  - ssh keys (without password) are encripted with ecryptfs

- without encripted partition
  - ssh keys with password, stored elsewhere (smartphone, etc)

- only with a ssh client
  - connect to "lifesaver" user with ssh
  - obtain admin rights with "sudo -i -u **user-admin**" using a unix user-admin password


Role Variables
--------------

      auth_disable_root: no           # disable root local and ssh login  
      
      auth_sysadmin_users:            # add this users as administratos
        - {name: calogero, uid: 5010, pass: PASSWORD }
        - {name: lillo, uid: 5011, pass: PASSWORD }  
      
      auth_remote_ssh_user: calogero  # user to use in ssh connection  
      
      auth_sysadmin_group: admins     # administration group name
      auth_sysadmin_gid: 2000         # administration group GID  
      
      auth_lifesaver_user: lifesaver  # name of "lifesaver" user
      auth_lifesaver_pass: PASSWORD
      auth_lifesaver_uid: 3000
      auth_lifesaver_shell: /bin/rbash  
      
      auth_sshd_port: 22              # ssh daemon port (default: 22)  
      
      # local user name (useful to create ssh keys)
      auth_local_user: calogero-local

      # local user home directory
      auth_local_home: /home/calogero-local

      # local user ssh directory
      auth_local_ssh_dir: "{{ auth_local_home }}/.ssh"

      # local user ssh config file
      auth_local_conf: "{{ auth_local_ssh_dir }}/config"

      # local user ssh config per host
      auth_local_conf_dir: "{{ auth_local_ssh_dir }}/.confs"


Examples
========

    - hosts: example
      roles:
         - { role: kalos.auth }

License
-------

GPLv3

Author Information
------------------

Calogero Lo Leggio (kalos) - [LibreIT](http://LibreIT.org)
