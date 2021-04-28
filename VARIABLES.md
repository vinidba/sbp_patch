# Explanation about the variables used on Ansible

First I am explaning about all variables, in the second section of file we have the parameters itself defined as of today in my customer environment:

## [SBP_files_source](#SBP_files_source)

SBP_files_source is the directory that have all the required files for SBP organized in the following structure:

- SBP_files_source
  - FEB_2021
    - SAP121XSP_2102-20012300.ZIP
    - PROACTIVEBP121XSP_2102-20012300.ZIP
    - OPATCH121XSP_2102-20012300.ZIP

The above files (12.1.0.2 patches) can be download at https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=N&FUNCTIONBAR=N&EVENT=TREE&TMPL=67838200100200024319&V=MAINT&TAB=info/

If you are planning to install SBP for 19c database, please refer to link https://launchpad.support.sap.com/#/softwarecenter/template/products/_APP=00200682500000001943&_EVENT=DISPHIER&HEADER=Y&FUNCTIONBAR=N&EVENT=TREE&NE=NAVIGATE&ENR=73555000100200011017&V=MAINT

## [SBP Files](#SBP_files)

SBP_Files is a list with some required info that will be used by Ansible playbook:

- sbp_patch_number: this is the number of Database Bundle Patch that can be found on README121XSP_2102-20012300.HTM file;
- sbp_patch_file: the name of SBP file;
- sbp_dbbp_patch_file: the name of Database Bundle Patch (dbbp) file;
- sbp_opatch_file: the name of OPatch file;
- sbp_month_year: the month and year in format YYYYMM, this must be the folder name that you need to create on [SBP_files_source](#SBP_files_source);
- sbp_patch_name: is the path name that must be used to define which subdirectories will be created. On README121XSP_2102-20012300.HTM you can found it on "This SBP includes all fixes listed in document BUGSFIXED121XSP_2102-20012300.HTM Bugs Fixed by Oracle® Database SAP® Bundle Patch 12.1.0.2.210119 - 202102, which is also contained in the SBP zip file SAP121XSP_2102-20012300.ZIP under the path **SBP_12102210119_202102**/9458152/bugs_fixed.html. SAP Bundle Patches are cumulative. That is, the latest SBP includes the fixes of all previous SBPs."
- sbp_post_install_sql: you need to create this file under sbp_month_year directory with all the "alter system set" commands changing parameters as requested on README121XSP_2102-20012300.HTM.

## [SBP_Hosts](#SBP_hosts)

SBP_Hosts is a list with all the info related to the hosts that you are planning to apply the patch:

- sbp_hostname: the hostname itself of database server;
- sbp_oracle_user: the OS user that owns the database;
- sbp_db_name: the name of SID;
- sbp_listener_name: the name of Listener that must be stopped/started during the patch activities;
- sbp_oracle_home: the path for Oracle Home where DB and Listener runs;
- sbp_oracle_base: the path for Oracle Base.

## [Global Actions](#Global_actions)

ga_sbp_patch_number: you need to specify which patch number that you are planning to apply. All the related files to this patch number must exists on [SBP_Files](#SBP_files) list;

## [Veritas Group Name](#Veritas_group_name)

veritas_group_name: is the group name of Oracle resources on Veritas Cluster if exists.

## [Is Customer Specific](#is_customer_specific)

is_customer_specific: define if some checks inside roles are specific for the customer. Actually only the file [https://github.com/vinidba/sbp_patch/blob/master/roles/validate_oracle_home/tasks/main.yml](https://github.com/vinidba/sbp_patch/blob/master/roles/validate_oracle_home/tasks/main.yml) have specific validations (if a specific mountpoint is mounted over a NFS).

# Contents of file:
--------------------------------------------------------------------------------------------------------------------------------


    # SBP Files Directory
    SBP_files_source: /log/viniciusdba/Ansible/SBP_Patching_Files

    # Patch related files
    SBP_Files:
    - { sbp_patch_number: '31511219', sbp_patch_file: 'SAP121XSP_2011-20012300.ZIP', sbp_dbbp_patch_file: 'PROACTIVEBP121XSP_2011-20012300.ZIP', sbp_opatch_file: 'OPATCH121XSP_2011-20012319.ZIP', sbp_month_year: 'NOV_2020' , sbp_patch_name: 'SBP_12102201020_202011', sbp_post_install_sql: 'post-install.sql' }
    - { sbp_patch_number: '31965033', sbp_patch_file: 'SAP121XSP_2102-20012300.ZIP', sbp_dbbp_patch_file: 'PROACTIVEBP121XSP_2102-20012300.ZIP', sbp_opatch_file: 'OPATCH121XSP_2102-20012300.ZIP', sbp_month_year: 'FEB_2021' , sbp_patch_name: 'SBP_12102210119_202102', sbp_post_install_sql: 'post-install.sql' }

    # Variables for Hosts to have patch applied
    SBP_Hosts:
    - { sbp_hostname: 'XXXDB1', sbp_oracle_user: 'ora1', sbp_db_name: 'DB1', sbp_listener_name: 'LISTENER_DB1', sbp_oracle_home: '/oracle/DB1/121', sbp_oracle_base: '/oracle/DB1/saptrace' }
    - { sbp_hostname: 'XXXDB2', sbp_oracle_user: 'ora2', sbp_db_name: 'DB2', sbp_listener_name: 'LISTENER_DB2', sbp_oracle_home: '/oracle/DB2/121', sbp_oracle_base: '/oracle/DB2/saptrace' }
    - { sbp_hostname: 'XXXDB3', sbp_oracle_user: 'ora3', sbp_db_name: 'DB3', sbp_listener_name: 'LISTENER_DB3', sbp_oracle_home: '/oracle/DB3/121', sbp_oracle_base: '/oracle/DB3/saptrace' }
    
    # Global Actions | Valid values for ga_sbp_action: 'Rollback' or 'Apply'
    ga_sbp_patch_number: '31511219'

    # Veritas Group Name
    veritas_group_name: 'ORA_1'

    # Is Customer Specific
    is_customer_specific: 'Y'
