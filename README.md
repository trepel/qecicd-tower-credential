# Tower Dummy Credentials Repo

Dummy Credentials repository to demonstrate how to bootstrap a tower instance with CI/CD jobs.

## Getting Started

This project serves as the inventory source for the [Ansible Tower Configuration](https://github.com/integr8ly/ansible-tower-configuration) project. As this project contains sensitive information, it should only be used locally.

## Usage

The example below runs the `bootstrap.yml` playbook using this project as the inventory source, where `--ask-vault-pass` is the password used to access the encrypted variables.

```bash
ansible-playbook -i <path-to-tower-dummy-credentials-project> playbooks/bootstrap.yml -e tower_host=<tower-host> -e tower_username=<tower-username> -e tower_password=<tower-password> -e tower_environment=<tower-environment> --ask-vault-pass
```

## Ansible-Vault

Credentials stored in this project are encrypted using [Ansible Vault](https://docs.ansible.com/ansible/latest/user_guide/vault.html). Ansible vault supports the encryption of entire [files](https://docs.ansible.com/ansible/latest/user_guide/vault.html#creating-encrypted-files), and also individual [string values](https://docs.ansible.com/ansible/latest/user_guide/vault.html#use-encrypt-string-to-create-encrypted-variables-to-embed-in-yaml) that can embedded within YAML files. The default encrypted string value of all variables within this project along with the password to decrypt these variables is `CHANGEME`.

### Files

Configuration variables required for the Openshift-Ansible installation process are contained within the `provisioning_vars.yml.j2` file. This file is encrypted using Ansible Vault and can be decrypted using the command below.

```bash
ansible-vault view provisioning_vars.yml.j2
```

### String Values

Individual variables within files can be decrypted using the command below,  where `--ask-vault-pass` is the encrypted variables password.

```bash
ansible localhost -m debug -a var='<variable_name>' -e '@<file_name>' --ask-vault-pass
```

## Setup

Each file in the project can contain one or more variables that need to be changed. Variables that need to be changed have a default value of `<CHANGEME>`.

A list of all variables that need to be changed along with their usage can be found [HERE](VARIABLES.md).