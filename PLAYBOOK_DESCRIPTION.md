# playbook_description.md

## sbp_patch

### Ansible Playbook to Install SAP Bundle Patch on Oracle Databases

A very simple Ansible playbook that allows you to apply SAP Bundle Patches on Oracle Database.

The install_SBP.yml playbook contains the following roles:

    - { role: validate_oracle_home }
    - { role: validate_db_role }
    - { role: validate_disk_space }
    - { role: validate_patch_info }
    - { role: db_inventory_validation }
    - { role: manage_veritas_cluster, cluster_operation : 'freeze' }
    - { role: copy_opatch_and_patch_SBP }
    - { role: shutdown_listener }
    - { role: manage_mrp, mrp_mode: 'stop' }
    - { role: shutdown_db, artifact: '1' }
    - { role: apply_mopatch_oracle_home }
    - { role: startup_db, startup_mode: 'nomount', create_pfile: 'Y' }
    - { role: startup_db, startup_mode: 'nomount', recreate_spfile: 'Y' }
    - { role: shutdown_db, artifact: '2' }
    - { role: rollback_opatch_oracle_home }
    - { role: startup_db, startup_mode: 'open', artifact: '1' }
    - { role: startup_db, startup_mode: 'mount', artifact: '1' }
    - { role: rollback_SBP_db } 
    - { role: post_install }
    - { role: shutdown_db, artifact: '3' }
    - { role: startup_db, startup_mode: 'open', artifact: '2' }
    - { role: startup_db, startup_mode: 'mount', artifact: '2' }
    - { role: startup_listener }
    - { role: rollback_opatch_and_mopatch }
    - { role: manage_mrp, mrp_mode: 'start' }
    - { role: manage_veritas_cluster, cluster_operation: 'unfreeze' }
    - { role: post_patch_check }


So, explaining one by one:

#### If you are applying/installing SBP:

- validate_oracle_home: will check if Oracle Home location is over a symbolic link pointing to a physical location. This is need to choose what location (link or physical) will be used during mopatch execution. This role also have a step to check if /sapcd is mounted over NFS. This is a customer specific definition that use this NFS mountpoint as stage to copy all required patch files. You can remove those lines before you execute the playbook.

- validate_db_role: will check the database role. If DB is a primary DB, all the steps will be executed (including the catsbp/datapatch step). If DB is a standby DB, catsbp will be skipped, also, MRP will be stopped and started during playbook execution.

- validate_disk_space: to check if disk has at least 15G of free space in order to copy the required patch files.

- validate_patch_info: will check if the desired patch is already installed on Oracle Home. If yes, it will end the playbook execution.

- db_inventory_validation: it will run a query on DB using DBMS_SQLPATCH package to check the integrity of inventory. If is not OK, it will end the playbook execution. This is used to avoid issues during catsbp/datapatch execution.

- manage_veritas_cluster: the client that I've worked uses Veritas Cluster to manage SAP environments. So, they have a Veritas Cluster consisting of two nodes for DB with "floatings" VIP, Listener and DB Instance. Their DBs are running on a NetApp storage, so, the NFS is already mounted on both nodes. This role will freeze the cluster before stop DB and listener. You need to specify the "ORA" cluster group name on group_vars/all.yml file (it will be explained later). This role will be executed only if DB Server has a Veritas Cluster running. If you don't have Veritas Cluster running, Ansible will skip this role for you.

- copy_opatch_and_patch_SBP: will copy the required patch files and extract them to the right locations.

- shutdown_listener: will shutdown listener.

- manage_mrp: if the DB is a standby, it will stop MRP.

- shutdown_db: it will shutdown DB.

- apply_mopatch_oracle_home: it will apply/install SBP on Oracle Home using mopatch utility.

- startup_db (create_pfile): it will start DB in nomount state in order to create a pfile from SPFILE before change parameters as required per SBP patch document. This pfile can be used as a rollback file for SPFILE.

- shutdown_db: it will shutdown DB again.

- startup_db: it will startup DB with OPEN mode as required before execute catsbp/datapatch.

- post_install: will execute catsbp/datapatch (only if DB is a primary DB) and change parameters as required per SBP patch document.

- shutdown_db: it will shutdown DB again.

- startup_db: it will startup DB again.

- startup_listener: it will start listener.

- manage_mrp: it will start MRP if DB is a standby.

- manage_veritas_cluster: it will unfreeze Veritas Cluster.

- post_patch_check: it will print the patches installed on Server (opatch lspatches) and the patches applied on DB (queries against DBA_REGISTRY_HISTORY and DBA_REGISTRY_SQLPATCH).

#### If you are removing/rollback SBP:

- validate_oracle_home: will check if Oracle Home location is over a symbolic link pointing to a physical location. This is need to choose what location (link or physical) will be used during rollback execution. This role also have a step to check if /sapcd is mounted over NFS. This is a customer specific definition that use this NFS mountpoint as stage to copy all required patch files. You can remove those lines before you execute the playbook.

- validate_db_role: will check the database role. If DB is a primary DB, all the steps will be executed (including the catsbp/datapatch step). If DB is a standby DB, catsbp will be skipped, also, MRP will be stopped and started during playbook execution.

- validate_disk_space: to check if disk has at least 15G of free space in order to copy the required patch files.

- validate_patch_info: will check if required files for rollback exists on $ORACLE_HOME/sapbundle subdirectories

- db_inventory_validation: it will run a query on DB using DBMS_SQLPATCH package to check the integrity of inventory. If is not OK, it will end the playbook execution. This is used to avoid issues during rmsbp/datapatch execution.

- manage_veritas_cluster: the client that I've worked uses Veritas Cluster to manage SAP environments. So, they have a Veritas Cluster consisting of two nodes for DB with "floatings" VIP, Listener and DB Instance. Their DBs are running on a NetApp storage, so, the NFS is already mounted on both nodes. This role will freeze the cluster before stop DB and listener. You need to specify the "ORA" cluster group name on group_vars/all.yml file (it will be explained later). This role will be executed only if DB Server has a Veritas Cluster running. If you don't have Veritas Cluster running, Ansible will skip this role for you.

- shutdown_listener: will shutdown listener.

- manage_mrp: if the DB is a standby, it will stop MRP.

- shutdown_db: it will shutdown DB.

- startup_db (recreate_spfile): it will start DB in nomount state in order to recreate the SPFILE from pfile created during patch installation. The pfile have the parameters before the change during patch installation.

- shutdown_db: it will shutdown DB again.

- rollback_opatch_oracle_home: it will execute opatch to rollback the patches.

- startup_db: it will startup DB with OPEN mode as required before execute rmsbp/datapatch.

- post_install: will execute rmsbp/datapatch (only if DB is a primary DB).

- shutdown_db: it will shutdown DB again.

- startup_db: it will startup DB again.

- startup_listener: it will start listener.

- rollback_opatch_and_mopatch: it will rollback OPatch and mopatch utilities to the previous version before patch installation.

- manage_mrp: it will start MRP if DB is a standby.

- manage_veritas_cluster: it will unfreeze Veritas Cluster.

- post_patch_check: it will print the patches installed/remoed on Server (opatch lspatches) and the patches applied on DB (queries against DBA_REGISTRY_HISTORY and DBA_REGISTRY_SQLPATCH).

