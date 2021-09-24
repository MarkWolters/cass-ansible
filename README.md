## Instructions
Clone the Playbook:
```
git clone https://github.com/MarkWolters/cass-ansible.git
```
Make sure you have SSH access to your hosts.

Have [Ansible](http://docs.ansible.com/intro_installation.html) installed on your machine. Requires Ansible 2.0 or higher.

Edit the ```hosts``` inventory file and add opscenter, seeds and non-seed sections.

To build a DSE cluster:

Run ```ansible-playbook -i inventory/hosts site.yml```

The playbook has the ability to download and 'stage' multiple versions of DSE via the variable 'stage_versions'. Once the DSE software has been staged, the current active version of the software can configured via the 'active_version' variable. This allows for very rapid upgrades of clusters using the playbook, by simply modifying the current 'active_version'.

```
dse:
  stage_versions: ["5.1.25, 6.8.14]
  active_version: "6.8.14"
```

### Enabling DSE Search and Analytics

To enable Spark on a node simply modify the inventory file :

```1.2.3.4 dc=Data1 rack=rack1 dse_spark=1```

To enable Solr on a node simply modify the inventory file :

```1.2.3.4 dse_solr=1```


### multi-dc deployments

Edit the ```hosts``` inventory file and simply specify the DC and Rack for the hosts. The inventory below will build a cluster with two DC's Data1 and Data2 with DSE Search enabled in Data1.

````
[seeds]
192.168.0.1 dc=Data1 rack=rack1 dse_search=1
192.168.10.1 dc=Data2 rack=rack1

[non-seeds]
192.168.0.2 dc=Data1 rack=rack2 dse_search=1
192.168.10.2 dc=Data2 rack=rack2
````

### Disk configuration
This playbook will configure DSE to store data, commitlogs and saved_caches under a directory called /db which will be owned by the user 'cassandra'. If the variable ````configure_disks```` is set to true and the parameter ````db_disks```` contains a comma separated list of block devices. Then this playbook will group the devices into a single volume group, create a logical volume and then mount a filesystem at ````/db````.   

````
configure_disks: false
db_disks: "/dev/xvdb,/dev/xvdc1,/dev/xvdc2,/dev/xvdc3"
````   

###  OpsCenter
This playbook will enable authentication to the OpsCenter UI, for details of the default user and password refer to the OpsCenter documentation above. This setting is controlled by the following variable:

````
opscenter:
  authentication: true
````

###  Precheck
This playbook will go to each host in the inventory and run the DataStax preflight_check tool. Results will be stored in a log file specified in configuration. See https://docs.datastax.com/en/dse/6.8/dse-dev/datastax_enterprise/tools/dsePreFlight.html for details on this tool. The location of the executable, location of log file and arguments to the command are configurable.

````
pfc_dir: /usr/share/dse/tools/pfc
pfc_args: 
pfc_log: /usr/share/dse/tools/pfc/log.txt
````

