# sbp_patch

## Ansible Playbook to Install SAP Bundle Patch on Oracle Databases

A very simple Ansible playbook that allows you to apply SAP Bundle Patches on Oracle Database.

To view an explanation for each role, please refer to file [PLAYBOOK_DESCRIPTION.md](https://github.com/vinidba/sbp_patch/blob/master/PLAYBOOK_DESCRIPTION.md).


### How to execute playbook to install SBP:

On your Ansible server go to your root playbook directory (eg):

```
$ cd install_SBP

$ ansible-playbook install_SBP.yml -e "ga_sbp_action=Apply"
```


### How to execute playbook to deinstall SBP:

On your Ansible server go to your root playbook directory (eg):

```
$ cd install_SBP

$ ansible-playbook install_SBP.yml -e "ga_sbp_action=Rollback"
```


### How to choose the hosts and to define the variables for the environment:

To select the hosts where you will run the playbook, edit the file [inventories/SAP_Prod/hosts](https://github.com/vinidba/sbp_patch/tree/master/inventories/SAP_Prod/hosts).

To define the variables, edit the file [inventories/SAP_Prod/group_vars/all.yml](https://github.com/vinidba/sbp_patch/tree/master/inventories/SAP_Prod/group_vars/all.yml).

To view an explanation for the variables file, please refer to file [VARIABLES.md](https://github.com/vinidba/sbp_patch/blob/master/VARIABLES.md).


### Ansible Configuration File

You can check all the configurations for Ansible on file [ansible.cfg](https://github.com/vinidba/sbp_patch/blob/master/ansible.cfg).


### General Notes

The intent of this playbook is to make your DBA life easier when you need to apply SBP on DB. For a specific customer, we were able to reduce the downtime window for 8 databases from 12 hours to 50 mins running the playbook in parallel.

This playbook does not have the intent to teach Ansible. If you are new on Ansible platform, I strongly recommend you to learn Ansible before execute this playbook on your environment.

You can reach me out anytime on https://www.viniciusdba.com.br/blog


## Latest change

* 21.01 (2021-04-28)
  - Initial Version.

Check **[CHANGELOG.md](https://github.com/vinidba/sbp_patch/blob/master/CHANGELOG.md)** for more info.