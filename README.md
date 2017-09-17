## Automate the deployment of MediaWiki

### Introduction:

Automation to configure and deploy MediaWiki.

### Assumptions:

* Provisioning complete.
	* The deployment topology was already provisioned using Cloudformation / Terraform / manually using AWS UI.)
	* The security groups have been configured have HTTP/HTTPS open for the node that runs loadbalancer. For all the other nodes appropriate ports are open to be accessed internally using `service-net`.
	* The AMI to be used is CentOS 7 offical image. (Refer: [CentOS Wiki / Cloud / AWS](https://wiki.centos.org/Cloud/AWS) for more information.) [ Tested on `ami-ae7bfdb8 / US-EAST-1 / CentOS Linux 7 x86_64 HVM EBS 1703_01` ]

* Ansible prep. complete.
	* The `environments/demo.env` are pre-populated with `service-net` IP addresses of the nodes as they will be run from jumpbox. The hostnames are given based on the roles played by the nodes.
	* The `group_vars/all` have the variables filled out with appropriate values.
	* The `ansible.cfg` is updated to use the appropriate path to SSH private key associated with that region/account.

### Structure:

There are 3 server roles applicable. A `db` role that runs and sets up MySQL server, `appserver` role that runs LAMP stack and MediaWiki application and `loadbalancer` role that runs HAProxy to loadbalance the appservers that run the MediaWiki application. 

### Running:

The command to be run for each role and the output to be expected ( for the first run ) are given below.

##### DB role:

````
[ansible]$ ansible-playbook -i environments/demo.env db.yml
````

##### APPSERVER role:

````
[ansible]$ ansible-playbook -i environments/demo.env appserver.yml
````

##### LOADBALANCER role:

````
[ansible]$ ansible-playbook -i environments/demo.env loadbalancer.yml
````

### Todo:

* ##### Security/Hardening
	* VPC Subnet segmentation for nodes based on their roles. ( Only loadbalancer role requires external IP, for all other roles a NAT-Gateway is sufficient ).
	* VPC Security groups appropriate for nodes based on their roles.
	* Host level firewall ( iptables )

* ##### High Availability
	* Using RDS Multi-AZ for Database HA.
	* Having appservers on different availability zones.
	* Having addtional loadbalancer on a different availability zone and have Elastic Loadbalancing - Network Loadbalancing setup amongst the loadbalancer nodes, so that an AZ failure can be tolerated.

* ##### Scalability*
	* PHP accelerators
	* Cache servers - Memcached/Redis
	* Autoscaling groups, etc.

*Scalability tuning depends on the traffic patterns and use case, but the above are the time-tested and recommended options that can be used to get started with.

### Author:

**Shyam Sundar C S**

csshyamsundar@gmail.com

