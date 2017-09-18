**DB playbook**

````
[ansible]$ ansible-playbook -i environments/demo.env db.yml

PLAY [gather facts of all hosts] *****************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************
ok: [demo-lb01]
ok: [demo-app02]
ok: [demo-app01]
ok: [demo-db01]

PLAY [configure and deploy the database] *********************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************
ok: [demo-db01]

TASK [base : ensure epel repo is installed] ******************************************************************************************************************
changed: [demo-db01]

TASK [base : ensure timezone] ********************************************************************************************************************************
changed: [demo-db01]

TASK [base : install the base packages] **********************************************************************************************************************
changed: [demo-db01] => (item=[u'htop', u'curl', u'wget', u'vim', u'git', u'ntp', u'libselinux-python'])

TASK [base : make selinux permissive] ************************************************************************************************************************
changed: [demo-db01]

TASK [base : update sysctl.conf] *****************************************************************************************************************************
changed: [demo-db01] => (item={u'dest': 299999, u'src': u'fs.file-max'})
changed: [demo-db01] => (item={u'dest': 0, u'src': u'net.ipv4.tcp_timestamps'})

TASK [base : setup limits.conf] ******************************************************************************************************************************
changed: [demo-db01]

TASK [base : setup ssh] **************************************************************************************************************************************
changed: [demo-db01]

TASK [base : ntp service state] ******************************************************************************************************************************
changed: [demo-db01]

TASK [mariadb : install the mariadb server and mysql-python] *************************************************************************************************
changed: [demo-db01] => (item=[u'MySQL-python', u'mariadb', u'mariadb-server'])

TASK [mariadb : mariadb service state] ***********************************************************************************************************************
changed: [demo-db01]

TASK [mariadb : update mariadb root password for all root accounts] ******************************************************************************************
changed: [demo-db01] => (item=%)
changed: [demo-db01] => (item=127.0.0.1)
changed: [demo-db01] => (item=::1)
changed: [demo-db01] => (item=localhost)

TASK [mariadb : setup .my.cnf file with root password credentials] *******************************************************************************************
changed: [demo-db01]

TASK [mariadb : delete anonymous user for localhost] *********************************************************************************************************
changed: [demo-db01]

TASK [mariadb : remove the mariadb test database] ************************************************************************************************************
changed: [demo-db01]

RUNNING HANDLER [base : reload sshd] *************************************************************************************************************************
changed: [demo-db01]

RUNNING HANDLER [base : reload sysctl] ***********************************************************************************************************************
changed: [demo-db01]

PLAY RECAP ***************************************************************************************************************************************************
demo-app01                 : ok=1    changed=0    unreachable=0    failed=0
demo-app02                 : ok=1    changed=0    unreachable=0    failed=0
demo-db01                  : ok=18   changed=16   unreachable=0    failed=0
demo-lb01                  : ok=1    changed=0    unreachable=0    failed=0
````

**APPSERVER playbook**

````
[ansible]$ ansible-playbook -i environments/demo.env appserver.yml

PLAY [gather facts of all hosts] *****************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************
ok: [demo-app01]
ok: [demo-lb01]
ok: [demo-app02]
ok: [demo-db01]

PLAY [configure and deploy the appserver] ********************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************
ok: [demo-app01]
ok: [demo-app02]

TASK [base : ensure epel repo is installed] ******************************************************************************************************************
changed: [demo-app01]
changed: [demo-app02]

TASK [base : ensure timezone] ********************************************************************************************************************************
changed: [demo-app01]
changed: [demo-app02]

TASK [base : install the base packages] **********************************************************************************************************************
changed: [demo-app01] => (item=[u'htop', u'curl', u'wget', u'vim', u'git', u'ntp', u'libselinux-python'])
changed: [demo-app02] => (item=[u'htop', u'curl', u'wget', u'vim', u'git', u'ntp', u'libselinux-python'])

TASK [base : make selinux permissive] ************************************************************************************************************************
changed: [demo-app01]
changed: [demo-app02]

TASK [base : update sysctl.conf] *****************************************************************************************************************************
changed: [demo-app01] => (item={u'dest': 299999, u'src': u'fs.file-max'})
changed: [demo-app02] => (item={u'dest': 299999, u'src': u'fs.file-max'})
changed: [demo-app01] => (item={u'dest': 0, u'src': u'net.ipv4.tcp_timestamps'})
changed: [demo-app02] => (item={u'dest': 0, u'src': u'net.ipv4.tcp_timestamps'})

TASK [base : setup limits.conf] ******************************************************************************************************************************
changed: [demo-app02]
changed: [demo-app01]

TASK [base : setup ssh] **************************************************************************************************************************************
changed: [demo-app01]
changed: [demo-app02]

TASK [base : ntp service state] ******************************************************************************************************************************
changed: [demo-app01]
changed: [demo-app02]

TASK [apache_php : ensure scl repo is installed] *************************************************************************************************************
changed: [demo-app01]
changed: [demo-app02]

TASK [apache_php : install the httpd 2.4 and php 5.5 packages] ***********************************************************************************************
changed: [demo-app01] => (item=[u'httpd24-httpd', u'mariadb', u'php55', u'php55-php', u'php55-php-mbstring', u'php55-php-mysqlnd', u'php55-php-gd', u'php55-php-xml'])
changed: [demo-app02] => (item=[u'httpd24-httpd', u'mariadb', u'php55', u'php55-php', u'php55-php-mbstring', u'php55-php-mysqlnd', u'php55-php-gd', u'php55-php-xml'])

TASK [apache_php : httpd24-httpd service state] **************************************************************************************************************
changed: [demo-app01]
changed: [demo-app02]

TASK [mediawiki : install the base packages] *****************************************************************************************************************
changed: [demo-app01] => (item=[u'MySQL-python', u'mariadb'])
changed: [demo-app02] => (item=[u'MySQL-python', u'mariadb'])

TASK [mediawiki : add mediawiki directory] *******************************************************************************************************************
changed: [demo-app01] => (item=/opt/rh/httpd24/root/var/www/mediawiki)
changed: [demo-app02] => (item=/opt/rh/httpd24/root/var/www/mediawiki)

TASK [mediawiki : download and unarchive mediawiki] **********************************************************************************************************
changed: [demo-app01]
changed: [demo-app02]

TASK [mediawiki : setup httpd configuration file] ************************************************************************************************************
changed: [demo-app01] => (item={u'dest': u'/opt/rh/httpd24/root/etc/httpd/conf.d/mediawiki.conf', u'src': u'mediawiki.conf.j2'})
changed: [demo-app02] => (item={u'dest': u'/opt/rh/httpd24/root/etc/httpd/conf.d/mediawiki.conf', u'src': u'mediawiki.conf.j2'})

TASK [mediawiki : symlink mediawiki app under mediawiki home] ************************************************************************************************
changed: [demo-app01] => (item={u'dest': u'/opt/rh/httpd24/root/var/www/mediawiki/mediawiki', u'src': u'/opt/rh/httpd24/root/var/www/mediawiki/mediawiki-1.29.1'})
changed: [demo-app02] => (item={u'dest': u'/opt/rh/httpd24/root/var/www/mediawiki/mediawiki', u'src': u'/opt/rh/httpd24/root/var/www/mediawiki/mediawiki-1.29.1'})

TASK [mediawiki : httpd24-httpd service state] ***************************************************************************************************************
ok: [demo-app01]
ok: [demo-app02]

RUNNING HANDLER [base : reload sshd] *************************************************************************************************************************
changed: [demo-app01]
changed: [demo-app02]

RUNNING HANDLER [base : reload sysctl] ***********************************************************************************************************************
changed: [demo-app01]
changed: [demo-app02]

RUNNING HANDLER [mediawiki : reload httpd24-httpd] ***********************************************************************************************************
changed: [demo-app01]
changed: [demo-app02]

PLAY RECAP ***************************************************************************************************************************************************
demo-app01                 : ok=22   changed=19   unreachable=0    failed=0
demo-app02                 : ok=22   changed=19   unreachable=0    failed=0
demo-db01                  : ok=1    changed=0    unreachable=0    failed=0
demo-lb01                  : ok=1    changed=0    unreachable=0    failed=0
````
**LOADBALANCER playbook**

````
[ansible]$ ansible-playbook -i environments/demo.env loadbalancer.yml

PLAY [gather facts of all hosts] *****************************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************
ok: [demo-lb01]
ok: [demo-app01]
ok: [demo-db01]
ok: [demo-app02]

PLAY [configure and deploy the loadbalancer] *****************************************************************************************************************

TASK [Gathering Facts] ***************************************************************************************************************************************
ok: [demo-lb01]

TASK [base : ensure epel repo is installed] ******************************************************************************************************************
changed: [demo-lb01]

TASK [base : ensure timezone] ********************************************************************************************************************************
changed: [demo-lb01]

TASK [base : install the base packages] **********************************************************************************************************************
changed: [demo-lb01] => (item=[u'htop', u'curl', u'wget', u'vim', u'git', u'ntp', u'libselinux-python'])

TASK [base : make selinux permissive] ************************************************************************************************************************
changed: [demo-lb01]

TASK [base : update sysctl.conf] *****************************************************************************************************************************
changed: [demo-lb01] => (item={u'dest': 299999, u'src': u'fs.file-max'})
changed: [demo-lb01] => (item={u'dest': 0, u'src': u'net.ipv4.tcp_timestamps'})

TASK [base : setup limits.conf] ******************************************************************************************************************************
changed: [demo-lb01]

TASK [base : setup ssh] **************************************************************************************************************************************
changed: [demo-lb01]

TASK [base : ntp service state] ******************************************************************************************************************************
changed: [demo-lb01]

TASK [haproxy : install the haproxy package] *****************************************************************************************************************
changed: [demo-lb01]

TASK [haproxy : setup haproxy configuration file] ************************************************************************************************************
changed: [demo-lb01]

TASK [haproxy : haproxy service state] ***********************************************************************************************************************
changed: [demo-lb01]

RUNNING HANDLER [base : reload sshd] *************************************************************************************************************************
changed: [demo-lb01]

RUNNING HANDLER [base : reload sysctl] ***********************************************************************************************************************
changed: [demo-lb01]

RUNNING HANDLER [haproxy : reload haproxy] *******************************************************************************************************************
changed: [demo-lb01]

PLAY RECAP ***************************************************************************************************************************************************
demo-app01                 : ok=1    changed=0    unreachable=0    failed=0
demo-app02                 : ok=1    changed=0    unreachable=0    failed=0
demo-db01                  : ok=1    changed=0    unreachable=0    failed=0
demo-lb01                  : ok=16   changed=14   unreachable=0    failed=0
````