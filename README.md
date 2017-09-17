## Automate the deployment of MediaWiki

### Introduction:

Automation to configure and deploy MediaWiki.

### Assumptions:

* Provisioning completetion.
  * The deployment topology was already provisioned using Cloudformation / Terraform / manually using AWS UI.
  * The security groups have been configured have HTTP/HTTPS open `externally` for the node that runs loadbalancer and HTTP for the appservers `internally` and MySQL (3306) for the database server `internally`. ( Note: `externally` would mean open to the world and `internally` would mean open to `service-net`.)
  * AMI: CentOS 7 offical image. (Refer: [CentOS Wiki / Cloud / AWS](https://wiki.centos.org/Cloud/AWS) for more information.) [Tested on `ami-ae7bfdb8 / US-EAST-1 / CentOS Linux 7 x86_64 HVM EBS 1703_01`.]

* Ansible preparation completion.
  * The `environments/demo.env` are pre-populated with `service-net` IP addresses of the nodes as they will be run from jumpbox. The hostnames in that environment file are named based on the roles played by the nodes.
  * The `group_vars/all` are filled out with appropriate values.
  * The `ansible.cfg` is updated with appropriate path to SSH private key associated with that region/account.

### Structure:

There are 3 server playbooks applicable. A `db` playbook that sets up MySQL/MariaDB server, `appserver` playbook that sets up Apache HTTPd, PHP stack and MediaWiki application and finally the `loadbalancer` playbook that sets up HAProxy to loadbalance requests between the appservers that run the MediaWiki application. 

### Convergence:

The commands to be run for each playbook are given below. 

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

### Areas of improvement:

* ##### Security/Hardening
  * VPC Subnet segmentation for nodes based on their roles. (Only loadbalancer role requires external IP, for all other roles a NAT-Gateway is sufficient).
  * VPC Security groups appropriate for nodes based on their roles.
  * SSL configuration with appropriate ciphers at loadbalancer.
  * Host level firewall ( iptables ).
  * Setup appropriate SELinux contexts and re-enable it.

* ##### High Availability
  * Using RDS Multi-AZ for Database HA.
  * Having appservers on different availability zones.
  * Having addtional loadbalancer on a different availability zone and have Elastic Loadbalancing - Network Loadbalancing setup amongst the loadbalancer nodes, so that an AZ failure can be tolerated.

* ##### Scalability*
  * PHP accelerators.
  * Cache servers - Memcached/Redis.
  * Instance (re)sizing based on performance.
  * Autosetup of configuration (localsettings.php) for Autoscaling.

_*Scalability tuning depends on the traffic patterns, use case and handling specific bottlenecks, but the above are some of the usual options to get started with._

Apart from this, monitoring and metrics are to be gathered across the stack. 

### Author:

**Shyam Sundar C S**

[Email](mailto:csshyamsundar@gmail.com)
