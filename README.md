rootpw
=========

The role does the following on the managed servers:

* generates a random password on the control node
* updates root password on the managed node
* writes the root password in a encrypted file using ansible-vault on the control node

The password is generated using a pre-defined salt and stored in a YAML file in `host_vars/{{inventory_hostname}}.yml` containg one key named `root_pw`. One file per host will be generated.

Retrieving the root password for a given server:

```
ansible-vault view host_vars/myserver.yml
```

Requirements
------------

No requirements.

Role Variables
--------------

* `{{ bytes }}`: number of bytes returned in base64 by openssl when generating the password
* `{{ salt }}`: salt used to generate the password
* `{{ master_pw }}`: ansible-vault secret for the generated file(s) encrypted
* `{{ vault_directory }}`: where to store the encrypted file

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

Dependencies
------------

No dependency.

Example Playbook
----------------

Using variables stored in (vault) file:

``` 
  - hosts: servers
    roles:
    - { role: rootpw }
```

Giving the variables in the playbook:

``` 
  - hosts: servers
    roles:
    - { role: rootpw, salt: 'my_salt', master_pw: 'mysupersecretincleartext' }
```

License
-------

MIT

Author Information
------------------

jeremy.bertozzi@gmail.com
