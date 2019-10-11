Ansible Role: cfssl 
======================================

[![Build Status](https://travis-ci.org/entercloudsuite/ansible-cfssl.svg?branch=master)](https://travis-ci.org/entercloudsuite/ansible-cfssl)
[![Galaxy](https://img.shields.io/badge/galaxy-entercloudsuite.cfssl-blue.svg?style=flat-square)](https://galaxy.ansible.com/entercloudsuite/cfssl)  

Installs cfssl on Ubuntu 16.04 (Xenial)

## Requirements

This role requires Ansible 2.4 or higher.


# Roles Host roles
Keyring
in this node will create CA, all node cert and key will generate on this node and copy to node.
Node: give fact for cert generations.

```
+-----------+
|           | <-- node-0.cert node-0.key node-0...
|  node-0   |
|           |
+-----------+                                                +-------------+
                                                             |   Bastion   |
+-----------+                                                |             |
|           | <-- node-1.cert node-0.key node-0...           |             |
|  node-1   |                                                +-------------+
|           | 
+-----------+
   .....
+-----------+
|           | <-- node-n.cert node-0.key node-0...
| node-n    |
|           |
+-----------+


+-----------+
|           |
| keyring   |   <-- ca-key
|           |   <-- certificate generations
+-----------+   <-- node-0.cert node-0.key node-1.cert node-1.key node-n.cert node-n.key
```

## Role Variables

The role defines its variables in `defaults/main.yml`:  

**download_list**: define pakage will be installed in keyring host
```yaml
download_list:
  - "{{ cfssl_pkg.cfssl }}"
  - "{{ cfssl_pkg.cfssljson }}"
  - "{{ cfssl_pkg.cfssl_bundle }}"
  - "{{ cfssl_pkg.cfssl_certinfo }}"
  - "{{ cfssl_pkg.cfssl_newkey }}"
  - "{{ cfssl_pkg.cfssl_scan }}"
  - "{{ cfssl_pkg.mkbundle }}"
  - "{{ cfssl_pkg.multirootca }}"
```


**organizations:** Organizations Name
```
certificate_configurations_base_path: /etc/cfssl
certificate_configurations_base_path_organizations: "{{ certificate_configurations_base_path }}/{{ organizations }}"
certificate_base_path: "/etc/ssl/private/{{ organizations }}"
```

**keyring_host:** name of keyring host

**certificate_directive** cfssl configurations

## Example Playbook

Run with default vars:

    - hosts: all
      roles:
        - { role: ansible-cfssl }

## Testing

Tests are performed using [Molecule](http://molecule.readthedocs.org/en/latest/).

Install Molecule or use `docker-compose run --rm molecule` to run a local Docker container, based on the [enterclousuite/molecule](https://hub.docker.com/r/fminzoni/molecule/) project, from where you can use `molecule`.

1. Run `molecule create` to start the target Docker container on your local engine.  
2. Use `molecule login` to log in to the running container.  
3. Edit the role files.  
4. Add other required roles (external) in the molecule/default/requirements.yml file.  
5. Edit the molecule/default/playbook.yml.  
6. Define infra tests under the molecule/default/tests folder using the goos verifier.  
7. When ready, use `molecule converge` to run the Ansible Playbook and `molecule verify` to execute the test suite.  
Note that the converge process starts performing a syntax check of the role.  
Destroy the Docker container with the command `molecule destroy`.   

To run all the steps with just one command, run `molecule test`. 

In order to run the role targeting a VM, use the playbook_deploy.yml file for example with the following command: `ansible-playbook ansible-cfssl/molecule/default/playbook_deploy.yml -i VM_IP_OR_FQDN, -u ubuntu --private-key private.pem`.  

## License

MIT