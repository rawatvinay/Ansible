DB2
Simple role to install IBM DB2 Build Status

Requirements
You need a copy of DB2 compressed. The role can download from a remote URL or use a local copy of DB2 present on your ansible host.

ROOT ACCESS

This role can only be used with root access. DB2 can be installed without root but many features like creating instances are only available as root. Make sure you're installing with root user or using privillege escalation with sudo.

Role Variables
###DB2_BINARY

This hash controls how to send the DB2 binary to the remote hosts.

db2_binary.url: A URL to download db2 (dont set this if you want to use a local copy)

db2_binary.location: A path to save the remote file or to get the file if url wasn't defined

db2_binary.dest: Where the role should decompress DB2 on remote host.

db2_binary:
    url: https://mycompany.com/downloads/db2_10_5.tar.gz
    location: /ansible/files/db2_10.5.tar.gz
    dest: /tmp
Check how to download DB2 from a remote URL here

If you have a local copy use this example

###DB2_CREATES

The DB2 tar.gz usually creates a folder named server, but there are some binaries that creates different folders, for example DB2 Express C create a folder named expc. If your tar.gz creates a different folder, change this variable. For example:

db2_creates: expc
###DB2_PACKAGES

The list of packages that the role should install before running the installer. Leave as default unless you know what you're doing.

DB2 requires some packages to run properly on Linux, you can read more about this here

###RESP

This hash is used to customize the DB2 installation.

prod: Witch product will be installed

file: Where should install DB2

lic_agreement: Accept or decline the license (If decline the DB2 won't be installed)

install_type: Customizations of the products that will be installed

resp:
   prod: "DB2_SERVER_EDITION"
   file: "/opt/ibm/db2/V10.5"
   lic_agreement: "ACCEPT" # ACCEPT or DECLINE
   install_type: "TYPICAL" # TYPICAL, COMPACT, CUSTOM
For DB2 Express-C (Check this file ):

 resp:
   prod: "EXPRESS_C"
   file: "/opt/ibm/db2/V10.1"
   lic_agreement: "ACCEPT" # ACCEPT or DECLINE
   install_type: "TYPICAL" # TYPICAL, COMPACT, CUSTOM
NOTE: By using this role, you're accepting IBM license.

Dependencies
None

Example Playbook
All custom options are provided through an example file on examples folder

You need at least specify where to get DB2. Downloading db2

- hosts: servers
  roles:
     - db2
  vars:
    db2_binary:
      location: /ansible/files/db2_10.5.tar.gz
       dest: /tmp
###Installing DB2 without instances

In some cases, such as cluster failover deployment, you may wish to install only DB2 software and not create any instances. This can be achieved by specifying the create_instances: false variable like shown below.

vars:
  create_instances: false
The full example here

###Creating a custom instance

The instance will be created using all DB2 defaults, but you can customize it using the hash db2_instances

The full example here

db2_instances:
     - instance: "DB2INST"
       name: "myinstan"
       group_name: "myinadm"
       fenced_username: "myfenc1"
       fenced_group_name: "myfadm1"
db2_instances is a list of instances, you can create more than one, an example of two instances is found here

###Customizing Parameters

Both global and instance parameters can be customized.

Define hash dbm_params and set any key: value DB2 parameter. The key must be a valid DB2 parameter.

db2_instances:
    - instance: "DB2INST"
      name: "myinstan"
      group_name: "myinadm"
      fenced_username: "myfenc1"
      fenced_group_name: "myfadm1"
      dbm_params:
        intra_parallel: "YES"
        numdb: "20"
The full example here

Global parameters
Global parameters are provided by defining global_params hash.

global_params:
    db2_antijoin: "YES"
    db2fcmcomm: "TCPIP4"
The full example here

###Creating databases

By default this role won't create databases, if you want to, define the hash list databases

The full example is found here

databases:
    - name: mydb
      instance: db2inst1
    - name: otherdb
      instance: db2inst2
      codeset: "UTF-8"
      territory: "en"
      pagesize: "16384"
Disclaimer
There still some work to be done. There's no warranty that the role will work for you.
