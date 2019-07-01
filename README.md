# Tower Dummy Credentials Repo

Dummy Credentials repository to demonstrate how to bootstrap a tower instance with CI/CD jobs.

## Getting Started

This project serves as the inventory source for the [Ansible Tower Configuration](https://github.com/integr8ly/ansible-tower-configuration) project.

## Usage 

The projects `SAMPLE_CREDENTIAL_CONFIG.yml` file contains a list of variables which need to be changed to suit your own environment. Once these variables have been changed, the `bootstrap.yml` playbook is used to consume this configuration file,encrypt any variables containing sensitive information using the supplied Ansible vault password, and then place each of the variables into the relevant group_vars file.

## Setup

**NOTE**: This project should be forked and made private before proceeding with the following setup steps.

1. Clone this project

```bash
git clone https://github.com/<forked_repository_org>/tower_dummy_credentials
```

2. Copy and rename the `SAMPLE_CREDENTIAL_CONFIG.yml` file locally. As the local copy of the config will sensitive information in plaintext, it should only be stored and referenced locally, and caution taken to not check it into a public repository.

```bash
cp SAMPLE_CREDENTIAL_CONFIG.yml local_credentials_config.yml
```

3. Change the variable values in the newly created local copy of the `local_credentials_config.yml` file. A list of all variables that need to be changed along with their usage can be found [HERE](VARIABLES.md).

4. From the projects root directory, run the `bootstrap.yml` playbook, specifying the path to your local copy of the credentials file.

```bash
ansible-playbook -i ./inventories/hosts bootstrap.yml --extra-vars='@local_credentials_config.yml'
```

## Adding new variables

The following steps outline the process of adding new variables to the project.

### Encrypted

1. Add the variable to the  `SAMPLE_CREDENTIAL_CONFIG.yml` file with a value of `<CHANGEME>`.

2. Add an entry within the `with_items` section of the [bootstrap_credentials.yml](roles/credentials/tasks/bootstrap_credentials.yml#L13-L39) file, replacing `new_variable` with the name of the new variable.

```bash
 - { name: 'new_variable', value: '{{ new_variable }}' }
 ```

3. Add the new variable to the relevant file template located in `roles/credentials/templates` using the below format, ensuring that the variable to substitute in the template has `_enc` appended to the end of the variable name.

```bash
new_variable: !vault
|
{{ new_variable_enc }}
 ```

 ### Plaintext

 1. Add the variable to the  `SAMPLE_CREDENTIAL_CONFIG.yml` file with a value of `<CHANGEME>`.
   
 2. Add the new variable to the relevant file template located in `roles/credentials/templates`, ensuring that the variables value is the name of the variable, placed within brackets.

```bash
 new_variable: {{ new_variable }}
 ```

## Decryption

Encrypted files and variables can be decrypted using the commands below, where the password is the `vault-password` variable value specified in your local copy of the `SAMPLE_CREDENTIAL_CONFIG.yml` file.

### Files

```bash
ansible-vault decrypt '<path-to-file-to-decrypt>'
 ```

### Variables

```bash
ansible localhost -m debug -a var='<variable-name>' -e '@<path-to-file>' --ask-vault-pass
 ```
