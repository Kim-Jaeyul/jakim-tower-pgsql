# DB2로 failover 시나리오

1. DB1 Service Down

   ```shell
   [root@bastion atower-pgsql]# ssh db1
   Activate the web console with: systemctl enable --now cockpit.socket
   
   This system is not registered to Red Hat Insights. See https://cloud.redhat.com/
   To register this system, run: insights-client --register
   
   Last login: Tue Feb  9 06:18:26 2021 from 192.168.1.30
   [root@db1 ~]#
   [root@db1 ~]# systemctl status postgresql.service
   ● postgresql.service - PostgreSQL database server
      Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
      Active: active (running) (thawing) since Tue 2021-02-09 05:50:04 KST; 51min ago
     Process: 36425 ExecStartPre=/usr/libexec/postgresql-check-db-dir postgresql (code=exited, status=0/SUCCESS)
    Main PID: 36428 (postmaster)
       Tasks: 30 (limit: 49295)
      Memory: 80.8M
      CGroup: /system.slice/postgresql.service
              ├─36428 /usr/bin/postmaster -D /var/lib/pgsql/data
              ├─36429 postgres: logger process
              ├─36431 postgres: checkpointer process
              ├─36432 postgres: writer process
              ├─36433 postgres: wal writer process
              ├─36434 postgres: autovacuum launcher process
              ├─36435 postgres: stats collector process
              ├─36436 postgres: bgworker: logical replication launcher
              ├─36455 postgres: awxdbuser1 tower1 192.168.1.33(49944) idle
              ├─36456 postgres: awxdbuser1 tower1 192.168.1.33(49946) idle
              ├─36457 postgres: awxdbuser1 tower1 192.168.1.32(42362) idle
              ├─36458 postgres: awxdbuser1 tower1 192.168.1.32(42364) idle
              ├─36460 postgres: awxdbuser1 tower1 192.168.1.31(55714) idle
              ├─36461 postgres: awxdbuser1 tower1 192.168.1.31(55716) idle
              ├─36462 postgres: awxdbuser1 tower1 192.168.1.33(49948) idle
              ├─36463 postgres: awxdbuser1 tower1 192.168.1.32(42366) idle
              ├─36464 postgres: awxdbuser1 tower1 192.168.1.31(55718) idle
              ├─38665 postgres: wal sender process replica 192.168.0.35(34956) streaming 0/4035CE8
              ├─40704 postgres: awxdbuser1 tower1 192.168.1.31(57192) idle
              ├─40740 postgres: awxdbuser1 tower1 192.168.1.32(43830) idle
              ├─40742 postgres: awxdbuser1 tower1 192.168.1.32(43834) idle
              ├─40808 postgres: awxdbuser1 tower1 192.168.1.33(51408) idle
              ├─40824 postgres: awxdbuser1 tower1 192.168.1.31(57224) idle
              ├─40842 postgres: awxdbuser1 tower1 192.168.1.33(51426) idle
              ├─40844 postgres: awxdbuser1 tower1 192.168.1.31(57234) idle
              ├─40846 postgres: awxdbuser1 tower1 192.168.1.32(43848) idle
              ├─40848 postgres: awxdbuser1 tower1 192.168.1.33(51430) idle
              ├─40850 postgres: awxdbuser1 tower1 192.168.1.31(57238) idle
              ├─40852 postgres: awxdbuser1 tower1 192.168.1.32(43852) idle
              └─40855 postgres: awxdbuser1 tower1 192.168.1.33(51434) idle
   
   Feb 09 05:50:04 db1.example.com systemd[1]: Starting PostgreSQL database server...
   Feb 09 05:50:04 db1.example.com postmaster[36428]: 2021-02-09 05:50:04.463 KST [36428] LOG:  listening on IPv4 a>
   Feb 09 05:50:04 db1.example.com postmaster[36428]: 2021-02-09 05:50:04.463 KST [36428] LOG:  listening on IPv6 a>
   Feb 09 05:50:04 db1.example.com postmaster[36428]: 2021-02-09 05:50:04.467 KST [36428] LOG:  listening on Unix s>
   Feb 09 05:50:04 db1.example.com postmaster[36428]: 2021-02-09 05:50:04.474 KST [36428] LOG:  listening on Unix s>
   Feb 09 05:50:04 db1.example.com postmaster[36428]: 2021-02-09 05:50:04.490 KST [36428] LOG:  redirecting log out>
   Feb 09 05:50:04 db1.example.com postmaster[36428]: 2021-02-09 05:50:04.490 KST [36428] HINT:  Future log output >
   Feb 09 05:50:04 db1.example.com systemd[1]: Started PostgreSQL database server.
   [root@db1 ~]#
   [root@db1 ~]# systemctl stop postgresql.service
   [root@db1 ~]# systemctl status postgresql.service
   ● postgresql.service - PostgreSQL database server
      Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
      Active: inactive (dead) (thawing) since Tue 2021-02-09 06:41:39 KST; 3s ago
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
   
   ```

2. Tower web console 상태확인

   ![image-20210209064254259](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/pic/image-20210209064254259.png)

   ![image-20210209064304227](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/pic/image-20210209064304227.png)

   ![image-20210209064324606](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/pic/image-20210209064324606.png)

3. DB2로 failover 수행

   ```shell
   [root@db1 ~]# exit
   logout
   Connection to db1 closed.
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# pwd
   /root/atower-pgsql
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# ansible-playbook -i hosts pgsql-promote.yml
   
   PLAY [Promote secondary HA database to primary] *****************************************************************
   
   TASK [Gathering Facts] ******************************************************************************************
   ok: [db2]
   
   TASK [postgres : include_vars] **********************************************************************************
   ok: [db2] => (item=/root/atower-pgsql/roles/postgres/vars/../vars/RedHat-8.yml)
   
   TASK [postgres : include_tasks] *********************************************************************************
   skipping: [db2]
   
   TASK [postgres : include_tasks] *********************************************************************************
   skipping: [db2]
   
   TASK [determine path for pg_ctl command] ************************************************************************
   ok: [db2]
   
   TASK [if Tower postgres role does not provide an SCL prefix] ****************************************************
   skipping: [db2]
   
   TASK [pgsql-replication : Get PostgreSQL version] ***************************************************************
   ok: [db2]
   
   TASK [pgsql-replication : Set PGSQL version] ********************************************************************
   ok: [db2]
   
   TASK [Check recovery.conf] **************************************************************************************
   ok: [db2]
   
   TASK [Exit if secondary is already promoted or not in standby mode] *********************************************
   skipping: [db2]
   
   TASK [Promote secondary PostgreSQL server to primary] ***********************************************************
   changed: [db2]
   
   TASK [ensure replica is not in recovery mode] *******************************************************************
   ok: [db2]
   
   TASK [check that recovery mode is off] **************************************************************************
   ok: [db2] => {
       "msg": "Recovery mode is False"
   }
   
   PLAY RECAP ******************************************************************************************************
   db2                        : ok=9    changed=1    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0
   
   [root@bastion atower-pgsql]#
   ```

4. DB2로 failover 후, tower에 다시 re-connect(방법1)

   ```
   [root@tower1 ~]# #### Before ####
   [root@tower1 ~]# cat /etc/tower/conf.d/postgres.py
   # Ansible Tower database settings.
   
   DATABASES = {
      'default': {
          'ATOMIC_REQUESTS': True,
          'ENGINE': 'awx.main.db.profiled_pg',
          'NAME': 'tower1',
          'USER': 'awxdbuser1',
          'PASSWORD': """redhat123""",
          'HOST': 'db1',
          'PORT': '5432',
          'OPTIONS': { 'sslmode': 'prefer',
                       'sslrootcert': '/etc/pki/tls/certs/ca-bundle.crt',
          },
      }
   }
   
   [root@tower1 ~]# #### After #### 
   [root@tower1 ~]# cat /etc/tower/conf.d/postgres.py
   # Ansible Tower database settings.
   
   DATABASES = {
      'default': {
          'ATOMIC_REQUESTS': True,
          'ENGINE': 'awx.main.db.profiled_pg',
          'NAME': 'tower1',
          'USER': 'awxdbuser1',
          'PASSWORD': """redhat123""",
          'HOST': 'db2',
          'PORT': '5432',
          'OPTIONS': { 'sslmode': 'prefer',
                       'sslrootcert': '/etc/pki/tls/certs/ca-bundle.crt',
          },
      }
   }
   
   [root@tower1 ~]#
   [root@tower1 ~]# ansible-tower-service restart
   [root@tower1 ~]# ansible-tower-service status
   [root@tower1 ~]# 
   [root@tower1 ~]# ###### Tower 1~3 순으로 모두 수행 ############
   ```

5. DB2로 failover 후, tower에 다시 re-connect(방법2)

   ```
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# pwd
   /root/atower-pgsql/ansible-tower-setup-bundle-3.8.1-1
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# cat inventory_failover
   [tower]
   tower1 ansible_host=192.168.1.31
   tower2 ansible_host=192.168.1.32
   tower3 ansible_host=192.168.1.33
   
   [database]
   
   [all:vars]
   admin_password='redhat123'
   
   pg_host='db2'
   pg_port='5432'
   
   pg_database='tower1'
   pg_username='awxdbuser1'
   pg_password='redhat123'
   
   rabbitmq_port=5672
   
   rabbitmq_username=tower
   rabbitmq_password='redhat123'
   rabbitmq_cookie=secretvalue
   rabbitmq_vhost=tower
   rabbitmq_use_long_name=true
   
   # 80/443 4369/TCP 5672/TCP 25672/TCP 15672/TCP 5432/TCP
   # Isolated Tower nodes automatically generate an RSA key for authentication;
   # To disable this behavior, set this value to false
   # isolated_key_generation=true
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# diff inventory_failover inventory
   11c11
   < pg_host='db2'
   ---
   > pg_host='db1'
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# cp inventory_failover inventory
   cp: overwrite 'inventory'? y
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# diff inventory_failover inventory
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# ./setup.sh
   ...
   PLAY RECAP ******************************************************************************************************
   tower1                     : ok=159  changed=30   unreachable=0    failed=0    skipped=82   rescued=0    ignored=0
   tower2                     : ok=148  changed=27   unreachable=0    failed=0    skipped=75   rescued=0    ignored=0
   tower3                     : ok=148  changed=27   unreachable=0    failed=0    skipped=75   rescued=0    ignored=0
   
   The setup process completed successfully.
   Setup log saved to /var/log/tower/setup-2021-02-09-06:55:35.log.
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   ```

6. Tower & DB2(primary) 연결확인

   ![image-20210209070010608](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/pic/image-20210209070010608.png)

   ![image-20210209070018217](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/pic/image-20210209070018217.png)

   ![image-20210209070025145](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/pic/image-20210209070025145.png)
