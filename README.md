rootpw
=========

The role does the following:

* generates a random password on the control node
* writes the root password in a encrypted file using ansible-vault on the control node
* updates root password on the managed node

The password is generated using a pre-defined salt and is stored in a YAML file in `{{ vault_directory }}/{{inventory_hostname}}.yml` containg one key named `root_pw`. One file per host will be generated.

Retrieving the root password for a given server:

```
ansible-vault view passwords/myserver.yml
```

The role has been written to make sure the password set on the target servers is aligned with the one written in the vault files. The files are backed-up as a precaution. 

Not setting `force_handlers: true` in the play can lead to cases where an unrelated failure can leave the `{{ master_pw }}` written in a plain text on the control node.

Requirements
------------

No requirements.

Role Variables
--------------

* `{{ bytes }}`: number of bytes returned in base64 by openssl when generating the password
* `{{ salt }}`: salt used to generate the password
* `{{ master_pw }}`: ansible-vault secret for the generated file(s) encrypted
* `{{ vault_directory }}`: where to store the encrypted file(s)

You probably want those variables in a file protected by Ansible Vault:

```
cd rootpw
cat <<EOF>defaults/main.yml
salt: toto
master_pw: tata
EOF
ansible-vault encrypt defaults/main.yml
New Vault password:                                                                                                   
```

It is recommended to avoid using any default Ansible variable directory like 'hosts_var', 'group_vars' or 'vars' for the variable `{{ vault_directory }}`.

Dependencies
------------

No dependency.

Example Playbook
----------------

Using variables stored in vault file (recommended):

``` 
cd rootpw
cat <<EOF>defaults/main.yml
salt: toto
master_pw: tata
EOF
ansible-vault encrypt defaults/main.yml
New Vault password:
cat playbook.yml
---
- name: apply rootpw role
  hosts: servers
  force_handlers: true
  roles:
  - { role: rootpw }
```

Giving the variables in the playbook:

```
---
- name: apply rootpw role
  hosts: servers
  force_handlers: true
  roles:
  - { role: rootpw, salt: 'my_salt', master_pw: 'mysupersecretincleartext' }
```

License
-------

MIT

Author Information
------------------

jeremy.bertozzi@gmail.com
