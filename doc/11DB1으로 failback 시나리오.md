# DB1으로 failback 시나리오

1. DB1의 postgresql 서비스 재시작

   ```shell
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# ssh db1
   Activate the web console with: systemctl enable --now cockpit.socket
   
   This system is not registered to Red Hat Insights. See https://cloud.redhat.com/
   To register this system, run: insights-client --register
   
   Last login: Tue Feb  9 06:40:26 2021 from 192.168.1.30
   [root@db1 ~]#
   [root@db1 ~]# systemctl status postgresql.service
   ● postgresql.service - PostgreSQL database server
      Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
      Active: inactive (dead) (thawing) since Tue 2021-02-09 06:41:39 KST; 23min ago
     Process: 36428 ExecStart=/usr/bin/postmaster -D ${PGDATA} (code=exited, status=0/SUCCESS)
     Process: 36425 ExecStartPre=/usr/libexec/postgresql-check-db-dir postgresql (code=exited, status=0/SUCCESS)
    Main PID: 36428 (code=exited, status=0/SUCCESS)
   
   Feb 09 05:50:04 db1.example.com postmaster[36428]: 2021-02-09 05:50:04.463 KST [36428] LOG:  listening on IPv6 a>
   Feb 09 05:50:04 db1.example.com postmaster[36428]: 2021-02-09 05:50:04.467 KST [36428] LOG:  listening on Unix s>
   Feb 09 05:50:04 db1.example.com postmaster[36428]: 2021-02-09 05:50:04.474 KST [36428] LOG:  listening on Unix s>
   Feb 09 05:50:04 db1.example.com postmaster[36428]: 2021-02-09 05:50:04.490 KST [36428] LOG:  redirecting log out>
   Feb 09 05:50:04 db1.example.com postmaster[36428]: 2021-02-09 05:50:04.490 KST [36428] HINT:  Future log output >
   Feb 09 05:50:04 db1.example.com systemd[1]: Started PostgreSQL database server.
   Feb 09 06:41:39 db1.example.com systemd[1]: Stopping PostgreSQL database server...
   Feb 09 06:41:39 db1.example.com systemd[1]: postgresql.service: Killing process 36429 (postmaster) with signal S>
   Feb 09 06:41:39 db1.example.com systemd[1]: postgresql.service: Succeeded.
   Feb 09 06:41:39 db1.example.com systemd[1]: Stopped PostgreSQL database server.
   [root@db1 ~]#
   [root@db1 ~]# systemctl start postgresql.service
   [root@db1 ~]# systemctl status postgresql.service
   ● postgresql.service - PostgreSQL database server
      Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
      Active: active (running) (thawing) since Tue 2021-02-09 07:04:59 KST; 4s ago
     Process: 41179 ExecStartPre=/usr/libexec/postgresql-check-db-dir postgresql (code=exited, status=0/SUCCESS)
    Main PID: 41182 (postmaster)
       Tasks: 8 (limit: 49295)
      Memory: 17.1M
      CGroup: /system.slice/postgresql.service
              ├─41182 /usr/bin/postmaster -D /var/lib/pgsql/data
              ├─41184 postgres: logger process
              ├─41186 postgres: checkpointer process
              ├─41187 postgres: writer process
              ├─41188 postgres: wal writer process
              ├─41189 postgres: autovacuum launcher process
              ├─41190 postgres: stats collector process
              └─41191 postgres: bgworker: logical replication launcher
   
   Feb 09 07:04:59 db1.example.com systemd[1]: Starting PostgreSQL database server...
   Feb 09 07:04:59 db1.example.com postmaster[41182]: 2021-02-09 07:04:59.504 KST [41182] LOG:  listening on IPv4 a>
   Feb 09 07:04:59 db1.example.com postmaster[41182]: 2021-02-09 07:04:59.504 KST [41182] LOG:  listening on IPv6 a>
   Feb 09 07:04:59 db1.example.com postmaster[41182]: 2021-02-09 07:04:59.509 KST [41182] LOG:  listening on Unix s>
   Feb 09 07:04:59 db1.example.com postmaster[41182]: 2021-02-09 07:04:59.518 KST [41182] LOG:  listening on Unix s>
   Feb 09 07:04:59 db1.example.com postmaster[41182]: 2021-02-09 07:04:59.536 KST [41182] LOG:  redirecting log out>
   Feb 09 07:04:59 db1.example.com postmaster[41182]: 2021-02-09 07:04:59.536 KST [41182] HINT:  Future log output >
   Feb 09 07:04:59 db1.example.com systemd[1]: Started PostgreSQL database server.
   [root@db1 ~]#
   ```

2. DB2 -> DB1으로 replication

   ```shell
   [root@db1 ~]# exit
   logout
   Connection to db1 closed.
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# cd ..
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# pwd
   /root/atower-pgsql
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# ll
   total 379744
   -rw-r--r--. 1 root root     19966 Feb  9 05:14 ansible.cfg
   drwxr-xr-x. 7 root root       277 Feb  9 06:58 ansible-tower-setup-bundle-3.8.1-1
   -rw-r--r--. 1 root root 388792591 Feb  9 05:18 ansible-tower-setup-bundle-latest.el8.tar.gz
   -rw-r--r--. 1 root root      3747 Feb  9 05:14 check-pgsql-replication.yml
   -rw-r--r--. 1 root root       246 Feb  9 06:05 hosts
   -rw-r--r--. 1 root root       227 Feb  9 05:31 hosts_bak
   -rw-r--r--. 1 root root      2583 Feb  9 05:14 pgsql-promote.yml
   -rw-r--r--. 1 root root        15 Feb  9 05:14 README.md
   drwxr-xr-x. 5 root root        68 Feb  9 05:14 roles
   -rw-r--r--. 1 root root       226 Feb  9 05:14 setup-pgsql-replication.yml
   -rw-r--r--. 1 root root       487 Feb  9 05:14 tower-set-inventory-ha.yml
   -rw-r--r--. 1 root root      1673 Feb  9 05:14 tower-setup.yml
   -rw-r--r--. 1 root root       154 Feb  9 05:14 tower-start-services.yml
   -rw-r--r--. 1 root root       152 Feb  9 05:14 tower-stop-services.yml
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# cp hosts hosts_failback
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# vim hosts_failback
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# cat hosts_failback
   [tower]
   tower1 ansible_host=192.168.1.31
   tower2 ansible_host=192.168.1.32
   tower3 ansible_host=192.168.1.33
   
   [database]
   db2 pgsqlrep_role=master
   
   [database_replica]
   db1 pgsqlrep_role=replica
   
   [all:vars]
   pg_host='db2'
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# ansible-playbook -i hosts_failback setup-pgsql-replication.yml
   ...
   PLAY RECAP ******************************************************************************************************
   db1                        : ok=12   changed=5    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
   db2                        : ok=8    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   tower1                     : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   tower2                     : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   tower3                     : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   
   [root@bastion atower-pgsql]#
   ```

3. DB2 = primary, DB1 = standby 확인

   ```shell
   [root@bastion atower-pgsql]# ansible-playbook -i hosts_failback check-pgsql-replication.yml
   
   PLAY [database_replica] *****************************************************************************************
   
   TASK [Gathering Facts] ******************************************************************************************
   ok: [db1]
   
   TASK [set_fact] *************************************************************************************************
   skipping: [db1]
   
   TASK [check replication status and latency] *********************************************************************
   ok: [db1]
   
   TASK [extract datetime] *****************************************************************************************
   ok: [db1] => {
       "msg": "Replication latency is 4.067062"
   }
   
   TASK [ensure replica is in recovery mode] ***********************************************************************
   ok: [db1]
   
   TASK [check recovery mode is set to true on replica(s)] *********************************************************
   ok: [db1] => {
       "msg": "Recovery mode is TRUE"
   }
   
   PLAY [database] *************************************************************************************************
   
   TASK [Gathering Facts] ******************************************************************************************
   ok: [db2]
   
   TASK [set_fact] *************************************************************************************************
   skipping: [db2]
   
   TASK [ensure master is not in recovery mode] ********************************************************************
   ok: [db2]
   
   TASK [check recovery mode is set to false on primary] ***********************************************************
   ok: [db2] => {
       "msg": "Recovery mode is FALSE"
   }
   
   TASK [get master db  configured on tower nodes] *****************************************************************
   ok: [db2 -> 192.168.1.31]
   
   TASK [check configured db for tower nodes] **********************************************************************
   ok: [db2] => {
       "msg": "Configured db is db2"
   }
   
   PLAY RECAP ******************************************************************************************************
   db1                        : ok=5    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   db2                        : ok=5    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   
   [root@bastion atower-pgsql]#
   
   ```

4. DB1=primary, DB2=standby 로 promote

   ```shell
   [root@db2 ~]# systemctl status postgresql.service
   ● postgresql.service - PostgreSQL database server
      Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
      Active: active (running) (thawing) since Tue 2021-02-09 07:12:22 KST; 15min ago
     Process: 40379 ExecStartPre=/usr/libexec/postgresql-check-db-dir postgresql (code=exited, status=0/SUCCESS)
    Main PID: 40382 (postmaster)
       Tasks: 29 (limit: 49296)
      Memory: 92.9M
      CGroup: /system.slice/postgresql.service
              ├─40382 /usr/bin/postmaster -D /var/lib/pgsql/data
              ├─40383 postgres: logger process
              ├─40385 postgres: checkpointer process
              ├─40386 postgres: writer process
              ├─40387 postgres: wal writer process
              ├─40388 postgres: autovacuum launcher process
              ├─40389 postgres: stats collector process
              ├─40390 postgres: bgworker: logical replication launcher
              ├─40408 postgres: awxdbuser1 tower1 192.168.1.33(58916) idle
              ├─40410 postgres: awxdbuser1 tower1 192.168.1.33(58918) idle
              ├─40412 postgres: awxdbuser1 tower1 192.168.1.32(57110) idle
              ├─40413 postgres: awxdbuser1 tower1 192.168.1.32(57112) idle
              ├─40414 postgres: awxdbuser1 tower1 192.168.1.31(39714) idle
              ├─40415 postgres: awxdbuser1 tower1 192.168.1.31(39716) idle
              ├─40416 postgres: awxdbuser1 tower1 192.168.1.33(58920) idle
              ├─40417 postgres: awxdbuser1 tower1 192.168.1.32(57114) idle
              ├─40418 postgres: awxdbuser1 tower1 192.168.1.31(39718) idle
              ├─43520 postgres: awxdbuser1 tower1 192.168.1.33(59312) idle
              ├─43542 postgres: awxdbuser1 tower1 192.168.1.31(40124) idle
              ├─43581 postgres: awxdbuser1 tower1 192.168.1.32(57538) idle
              ├─43644 postgres: awxdbuser1 tower1 192.168.1.32(57544) idle
              ├─43652 postgres: awxdbuser1 tower1 192.168.1.33(59358) idle
              ├─43658 postgres: awxdbuser1 tower1 192.168.1.33(59362) idle
              ├─43661 postgres: awxdbuser1 tower1 192.168.1.31(40156) idle
              ├─43662 postgres: awxdbuser1 tower1 192.168.1.32(57556) idle
              ├─43663 postgres: awxdbuser1 tower1 192.168.1.33(59364) idle
              ├─43664 postgres: awxdbuser1 tower1 192.168.1.32(57558) idle
              ├─43666 postgres: awxdbuser1 tower1 192.168.1.31(40160) idle
              └─43667 postgres: awxdbuser1 tower1 192.168.1.31(40162) idle
   
   Feb 09 07:12:22 db2.example.com systemd[1]: Starting PostgreSQL database server...
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.878 KST [40382] LOG:  listening on IPv4 a>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.878 KST [40382] LOG:  listening on IPv6 a>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.882 KST [40382] LOG:  listening on Unix s>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.888 KST [40382] LOG:  listening on Unix s>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.905 KST [40382] LOG:  redirecting log out>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.905 KST [40382] HINT:  Future log output >
   Feb 09 07:12:22 db2.example.com systemd[1]: Started PostgreSQL database server.
   [root@db2 ~]#
   [root@db2 ~]# systemctl stop postgresql.service
   [root@db2 ~]# systemctl status postgresql.service
   ● postgresql.service - PostgreSQL database server
      Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
      Active: inactive (dead) (thawing) since Tue 2021-02-09 07:28:00 KST; 4s ago
     Process: 40382 ExecStart=/usr/bin/postmaster -D ${PGDATA} (code=exited, status=0/SUCCESS)
     Process: 40379 ExecStartPre=/usr/libexec/postgresql-check-db-dir postgresql (code=exited, status=0/SUCCESS)
    Main PID: 40382 (code=exited, status=0/SUCCESS)
   
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.878 KST [40382] LOG:  listening on IPv6 a>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.882 KST [40382] LOG:  listening on Unix s>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.888 KST [40382] LOG:  listening on Unix s>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.905 KST [40382] LOG:  redirecting log out>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.905 KST [40382] HINT:  Future log output >
   Feb 09 07:12:22 db2.example.com systemd[1]: Started PostgreSQL database server.
   Feb 09 07:28:00 db2.example.com systemd[1]: Stopping PostgreSQL database server...
   Feb 09 07:28:00 db2.example.com systemd[1]: postgresql.service: Killing process 40383 (postmaster) with signal S>
   Feb 09 07:28:00 db2.example.com systemd[1]: postgresql.service: Succeeded.
   Feb 09 07:28:00 db2.example.com systemd[1]: Stopped PostgreSQL database server.
   [root@db2 ~]#
   [root@db2 ~]# exit
   logout
   Connection to db2 closed.
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# ansible-playbook -i hosts_failback pgsql-promote.yml
   
   PLAY [Promote secondary HA database to primary] *****************************************************************
   
   TASK [Gathering Facts] ******************************************************************************************
   ok: [db1]
   
   TASK [postgres : include_vars] **********************************************************************************
   ok: [db1] => (item=/root/atower-pgsql/roles/postgres/vars/../vars/RedHat-8.yml)
   
   TASK [postgres : include_tasks] *********************************************************************************
   skipping: [db1]
   
   TASK [postgres : include_tasks] *********************************************************************************
   skipping: [db1]
   
   TASK [determine path for pg_ctl command] ************************************************************************
   ok: [db1]
   
   TASK [if Tower postgres role does not provide an SCL prefix] ****************************************************
   skipping: [db1]
   
   TASK [pgsql-replication : Get PostgreSQL version] ***************************************************************
   ok: [db1]
   
   TASK [pgsql-replication : Set PGSQL version] ********************************************************************
   ok: [db1]
   
   TASK [Check recovery.conf] **************************************************************************************
   ok: [db1]
   
   TASK [Exit if secondary is already promoted or not in standby mode] *********************************************
   skipping: [db1]
   
   TASK [Promote secondary PostgreSQL server to primary] ***********************************************************
   changed: [db1]
   
   TASK [ensure replica is not in recovery mode] *******************************************************************
   ok: [db1]
   
   TASK [check that recovery mode is off] **************************************************************************
   ok: [db1] => {
       "msg": "Recovery mode is False"
   }
   
   PLAY RECAP ******************************************************************************************************
   db1                        : ok=9    changed=1    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
   
   [root@bastion atower-pgsql]#
   ```

5. DB1 = primary, DB2 = standby 다시 replication 설정

   ```shell
   [root@bastion atower-pgsql]# ssh db2
   Activate the web console with: systemctl enable --now cockpit.socket
   
   This system is not registered to Red Hat Insights. See https://cloud.redhat.com/
   To register this system, run: insights-client --register
   
   Last login: Tue Feb  9 07:27:15 2021 from 192.168.1.30
   [root@db2 ~]#
   [root@db2 ~]#
   [root@db2 ~]# systemctl status postgresql.service
   ● postgresql.service - PostgreSQL database server
      Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
      Active: inactive (dead) (thawing) since Tue 2021-02-09 07:28:00 KST; 5min ago
     Process: 40382 ExecStart=/usr/bin/postmaster -D ${PGDATA} (code=exited, status=0/SUCCESS)
     Process: 40379 ExecStartPre=/usr/libexec/postgresql-check-db-dir postgresql (code=exited, status=0/SUCCESS)
    Main PID: 40382 (code=exited, status=0/SUCCESS)
   
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.878 KST [40382] LOG:  listening on IPv6 a>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.882 KST [40382] LOG:  listening on Unix s>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.888 KST [40382] LOG:  listening on Unix s>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.905 KST [40382] LOG:  redirecting log out>
   Feb 09 07:12:22 db2.example.com postmaster[40382]: 2021-02-09 07:12:22.905 KST [40382] HINT:  Future log output >
   Feb 09 07:12:22 db2.example.com systemd[1]: Started PostgreSQL database server.
   Feb 09 07:28:00 db2.example.com systemd[1]: Stopping PostgreSQL database server...
   Feb 09 07:28:00 db2.example.com systemd[1]: postgresql.service: Killing process 40383 (postmaster) with signal S>
   Feb 09 07:28:00 db2.example.com systemd[1]: postgresql.service: Succeeded.
   Feb 09 07:28:00 db2.example.com systemd[1]: Stopped PostgreSQL database server.
   [root@db2 ~]#
   [root@db2 ~]# systemctl start postgresql.service
   [root@db2 ~]# systemctl status postgresql.service
   ● postgresql.service - PostgreSQL database server
      Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
      Active: active (running) (thawing) since Tue 2021-02-09 07:33:19 KST; 1s ago
     Process: 43832 ExecStartPre=/usr/libexec/postgresql-check-db-dir postgresql (code=exited, status=0/SUCCESS)
    Main PID: 43835 (postmaster)
       Tasks: 14 (limit: 49296)
      Memory: 31.2M
      CGroup: /system.slice/postgresql.service
              ├─43835 /usr/bin/postmaster -D /var/lib/pgsql/data
              ├─43837 postgres: logger process
              ├─43839 postgres: checkpointer process
              ├─43840 postgres: writer process
              ├─43841 postgres: wal writer process
              ├─43842 postgres: autovacuum launcher process
              ├─43843 postgres: stats collector process
              ├─43844 postgres: bgworker: logical replication launcher
              ├─43845 postgres: awxdbuser1 tower1 192.168.1.32(44460) idle
              ├─43848 postgres: awxdbuser1 tower1 192.168.1.31(55254) idle
              ├─43849 postgres: awxdbuser1 tower1 192.168.1.31(55256) idle
              ├─43850 postgres: awxdbuser1 tower1 192.168.1.31(55258) idle
              ├─43853 postgres: awxdbuser1 tower1 192.168.1.33(46202) idle
              └─43854 postgres: awxdbuser1 tower1 192.168.1.33(46204) idle
   
   Feb 09 07:33:18 db2.example.com systemd[1]: Starting PostgreSQL database server...
   Feb 09 07:33:19 db2.example.com postmaster[43835]: 2021-02-09 07:33:19.027 KST [43835] LOG:  listening on IPv4 a>
   Feb 09 07:33:19 db2.example.com postmaster[43835]: 2021-02-09 07:33:19.027 KST [43835] LOG:  listening on IPv6 a>
   Feb 09 07:33:19 db2.example.com postmaster[43835]: 2021-02-09 07:33:19.035 KST [43835] LOG:  listening on Unix s>
   Feb 09 07:33:19 db2.example.com postmaster[43835]: 2021-02-09 07:33:19.042 KST [43835] LOG:  listening on Unix s>
   Feb 09 07:33:19 db2.example.com postmaster[43835]: 2021-02-09 07:33:19.058 KST [43835] LOG:  redirecting log out>
   Feb 09 07:33:19 db2.example.com postmaster[43835]: 2021-02-09 07:33:19.058 KST [43835] HINT:  Future log output >
   Feb 09 07:33:19 db2.example.com systemd[1]: Started PostgreSQL database server.
   [root@db2 ~]#
   [root@db2 ~]# exit
   logout
   Connection to db2 closed.
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# ansible-playbook -i hosts setup-pgsql-replication.yml
   ...
   PLAY RECAP ******************************************************************************************************
   db1                        : ok=8    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   db2                        : ok=12   changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
   tower1                     : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   tower2                     : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   tower3                     : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   
   [root@bastion atower-pgsql]#
   ```

6. DB1로 failback 후, tower에 다시 re-connect(방법1)

   ```shell
   [root@bastion atower-pgsql]# 
   [root@bastion atower-pgsql]# cd ansible-tower-setup-bundle-3.8.1-1/
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# ll
   total 72
   -rw-r--r--.  1 root root   626 Jan 13 20:43 backup.yml
   drwxr-xr-x.  4 root root    28 Jan 13 20:44 bundle
   drwxr-xr-x.  3 root root    33 Jan 13 20:44 collections
   drwxr-xr-x.  2 root root    17 Jan 13 20:43 group_vars
   -rw-r--r--.  1 root root  8521 Jan 13 20:43 install.yml
   -rw-r--r--.  1 root root   628 Feb  9 06:54 inventory
   -rw-r--r--.  1 root root  2915 Feb  9 05:33 inventory_bak
   -rw-r--r--.  1 root root   628 Feb  9 06:52 inventory_failover
   -rw-r--r--.  1 root root   628 Feb  9 06:54 inventory_org
   drwxr-xr-x.  3 root root  8192 Jan 13 20:43 licenses
   -rw-r--r--.  1 root root  2506 Jan 13 20:43 README.md
   -rw-r--r--.  1 root root  1335 Jan 13 20:43 rekey.yml
   -rw-r--r--.  1 root root  3492 Jan 13 20:43 restore.yml
   drwxr-xr-x. 21 root root  4096 Jan 13 20:43 roles
   -rwxr-xr-x.  1 root root 10819 Jan 13 20:43 setup.sh
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# cp inventory_org inventory
   cp: overwrite 'inventory'? y
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# diff inventory inventory_failover
   11c11
   < pg_host='db1'
   ---
   > pg_host='db2'
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# ./setup.sh
   ...
   PLAY RECAP ******************************************************************************************************
   tower1                     : ok=159  changed=30   unreachable=0    failed=0    skipped=82   rescued=0    ignored=0
   tower2                     : ok=148  changed=27   unreachable=0    failed=0    skipped=75   rescued=0    ignored=0
   tower3                     : ok=148  changed=27   unreachable=0    failed=0    skipped=75   rescued=0    ignored=0
   
   The setup process completed successfully.
   Setup log saved to /var/log/tower/setup-2021-02-09-07:39:03.log.
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   ```

7. DB1 = primary, DB2 = standby 설정확인

   ```shell
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# cd ..
   [root@bastion atower-pgsql]# ansible-playbook -i hosts check-pgsql-replication.yml
   
   PLAY [database_replica] *****************************************************************************************
   
   TASK [Gathering Facts] ******************************************************************************************
   ok: [db2]
   
   TASK [set_fact] *************************************************************************************************
   skipping: [db2]
   
   TASK [check replication status and latency] *********************************************************************
   ok: [db2]
   
   TASK [extract datetime] *****************************************************************************************
   ok: [db2] => {
       "msg": "Replication latency is 13.222286"
   }
   
   TASK [ensure replica is in recovery mode] ***********************************************************************
   ok: [db2]
   
   TASK [check recovery mode is set to true on replica(s)] *********************************************************
   ok: [db2] => {
       "msg": "Recovery mode is TRUE"
   }
   
   PLAY [database] *************************************************************************************************
   
   TASK [Gathering Facts] ******************************************************************************************
   ok: [db1]
   
   TASK [set_fact] *************************************************************************************************
   skipping: [db1]
   
   TASK [ensure master is not in recovery mode] ********************************************************************
   ok: [db1]
   
   TASK [check recovery mode is set to false on primary] ***********************************************************
   ok: [db1] => {
       "msg": "Recovery mode is FALSE"
   }
   
   TASK [get master db  configured on tower nodes] *****************************************************************
   ok: [db1 -> 192.168.1.31]
   
   TASK [check configured db for tower nodes] **********************************************************************
   ok: [db1] => {
       "msg": "Configured db is db1"
   }
   
   PLAY RECAP ******************************************************************************************************
   db1                        : ok=5    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   db2                        : ok=5    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   
   [root@bastion atower-pgsql]#
   ```

   

