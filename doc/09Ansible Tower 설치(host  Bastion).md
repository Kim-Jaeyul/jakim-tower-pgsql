# Ansible Tower 설치(host : Bastion)

1. Download pgsql role

   ```shell
   [root@bastion ~]# git clone https://github.com/mh2ict/atower-pgsql.git
   Cloning into 'atower-pgsql'...
   remote: Enumerating objects: 3, done.
   remote: Counting objects: 100% (3/3), done.
   remote: Compressing objects: 100% (2/2), done.
   remote: Total 67 (delta 0), reused 3 (delta 0), pack-reused 64
   Unpacking objects: 100% (67/67), 26.44 KiB | 443.00 KiB/s, done.
   [root@bastion ~]#
   [root@bastion ~]# cd atower-pgsql/
   [root@bastion atower-pgsql]# ll
   total 56
   -rw-r--r--. 1 root root 19966 Feb  9 05:14 ansible.cfg
   -rw-r--r--. 1 root root  3747 Feb  9 05:14 check-pgsql-replication.yml
   -rw-r--r--. 1 root root   227 Feb  9 05:14 hosts
   -rw-r--r--. 1 root root  2583 Feb  9 05:14 pgsql-promote.yml
   -rw-r--r--. 1 root root    15 Feb  9 05:14 README.md
   drwxr-xr-x. 5 root root    68 Feb  9 05:14 roles
   -rw-r--r--. 1 root root   226 Feb  9 05:14 setup-pgsql-replication.yml
   -rw-r--r--. 1 root root   487 Feb  9 05:14 tower-set-inventory-ha.yml
   -rw-r--r--. 1 root root  1673 Feb  9 05:14 tower-setup.yml
   -rw-r--r--. 1 root root   154 Feb  9 05:14 tower-start-services.yml
   -rw-r--r--. 1 root root   152 Feb  9 05:14 tower-stop-services.yml
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# cd ..
   [root@bastion ~]# ll
   total 9624368
   -rw-------. 1 root root       1328 Feb  9 01:14 anaconda-ks.cfg
   drwxr-xr-x. 7 root root        209 Feb  9 03:39 ansible-tower-setup-bundle-3.8.1-1
   -rw-r--r--. 1 root root  388792591 Jan 13 20:46 ansible-tower-setup-bundle-latest.el8.tar.gz
   drwxr-xr-x. 4 root root       4096 Feb  9 05:14 atower-pgsql
   drwxr-xr-x. 2 root root          6 Feb  9 01:29 Desktop
   drwxr-xr-x. 2 root root          6 Feb  9 01:29 Documents
   drwxr-xr-x. 2 root root          6 Feb  9 01:29 Downloads
   -rw-r--r--. 1 root root       1600 Feb  9 01:16 initial-setup-ks.cfg
   drwxr-xr-x. 2 root root          6 Feb  9 01:29 Music
   drwxr-xr-x. 2 root root          6 Feb  9 01:29 Pictures
   drwxr-xr-x. 2 root root          6 Feb  9 01:29 Public
   -rw-r--r--. 1 root root 9466544128 Feb  9 01:54 rhel-8.3-x86_64-dvd.iso
   drwxr-xr-x. 2 root root          6 Feb  9 01:29 Templates
   drwxr-xr-x. 2 root root          6 Feb  9 01:29 Videos
   [root@bastion ~]# cp ansible-tower-setup-bundle-latest.el8.tar.gz atower-pgsql/
   [root@bastion ~]#
   [root@bastion ~]# cd atower-pgsql/
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# tar xzf ansible-tower-setup-bundle-latest.el8.tar.gz
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# ll
   total 379740
   -rw-r--r--. 1 root root     19966 Feb  9 05:14 ansible.cfg
   drwxr-xr-x. 7 root root       209 Jan 13 20:44 ansible-tower-setup-bundle-3.8.1-1
   -rw-r--r--. 1 root root 388792591 Feb  9 05:18 ansible-tower-setup-bundle-latest.el8.tar.gz
   -rw-r--r--. 1 root root      3747 Feb  9 05:14 check-pgsql-replication.yml
   -rw-r--r--. 1 root root       227 Feb  9 05:14 hosts
   -rw-r--r--. 1 root root      2583 Feb  9 05:14 pgsql-promote.yml
   -rw-r--r--. 1 root root        15 Feb  9 05:14 README.md
   drwxr-xr-x. 5 root root        68 Feb  9 05:14 roles
   -rw-r--r--. 1 root root       226 Feb  9 05:14 setup-pgsql-replication.yml
   -rw-r--r--. 1 root root       487 Feb  9 05:14 tower-set-inventory-ha.yml
   -rw-r--r--. 1 root root      1673 Feb  9 05:14 tower-setup.yml
   -rw-r--r--. 1 root root       154 Feb  9 05:14 tower-start-services.yml
   -rw-r--r--. 1 root root       152 Feb  9 05:14 tower-stop-services.yml
   [root@bastion atower-pgsql]#
   ```

2. 2개의 inventory file 수정

   ```shell
   [root@bastion atower-pgsql]# pwd
   /root/atower-pgsql
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# grep ^inventory ansible.cfg
   inventory      = hosts
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# cat << EOF > hosts
   > [tower]
   > tower1 ansible_host=192.168.1.31
   > tower2 ansible_host=192.168.1.32
   > tower3 ansible_host=192.168.1.33
   >
   > [database]
   > db1 pgsqlrep_role=master
   >
   > [database_replica]
   > db2 pgsqlrep_role=replica
   >
   > [all:vars]
   > pg_host='db1'
   > pgsqlrep_password='redhat123'
   > EOF
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# cat hosts
   [tower]
   tower1 ansible_host=192.168.1.31
   tower2 ansible_host=192.168.1.32
   tower3 ansible_host=192.168.1.33
   
   [database]
   db1 pgsqlrep_role=master
   
   [database_replica]
   db2 pgsqlrep_role=replica
   
   [all:vars]
   pg_host='db1'
   pgsqlrep_password='redhat123'
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# cat << EOF > ansible-tower-setup-bundle-3.8.1-1/inventory
   > [tower]
   > tower1 ansible_host=192.168.1.31
   > tower2 ansible_host=192.168.1.32
   > tower3 ansible_host=192.168.1.33
   >
   > [database]
   >
   > [all:vars]
   > admin_password='redhat123'
   >
   > pg_host='db1'
   > pg_port='5432'
   >
   > pg_database='tower1'
   > pg_username='awxdbuser1'
   > pg_password='redhat123'
   >
   > rabbitmq_port=5672
   >
   > rabbitmq_username=tower
   > rabbitmq_password='redhat123'
   > rabbitmq_cookie=secretvalue
   > rabbitmq_vhost=tower
   > rabbitmq_use_long_name=true
   >
   > # 80/443 4369/TCP 5672/TCP 25672/TCP 15672/TCP 5432/TCP
   > # Isolated Tower nodes automatically generate an RSA key for authentication;
   > # To disable this behavior, set this value to false
   > # isolated_key_generation=true
   > EOF
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# cat ansible-tower-setup-bundle-3.8.1-1/inventory
   [tower]
   tower1 ansible_host=192.168.1.31
   tower2 ansible_host=192.168.1.32
   tower3 ansible_host=192.168.1.33
   
   [database]
   
   [all:vars]
   admin_password='redhat123'
   
   pg_host='db1'
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
   [root@bastion atower-pgsql]#
   ```

3. Ansible Tower 설치 시작

   ```shell
   [root@bastion atower-pgsql]#
   [root@bastion atower-pgsql]# pwd
   /root/atower-pgsql
   [root@bastion atower-pgsql]# cd ansible-tower-setup-bundle-3.8.1-1/
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# ./setup.sh
   ...
   PLAY RECAP ******************************************************************************************************
   tower1                     : ok=158  changed=72   unreachable=0    failed=0    skipped=84   rescued=0    ignored=2
   tower2                     : ok=146  changed=64   unreachable=0    failed=0    skipped=78   rescued=0    ignored=1
   tower3                     : ok=146  changed=64   unreachable=0    failed=0    skipped=78   rescued=0    ignored=1
   
   The setup process completed successfully.
   Setup log saved to /var/log/tower/setup-2021-02-09-05:38:15.log.
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   ```

4. postgresql replication 세팅하기

   ```shell
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# cd ..
   [root@bastion atower-pgsql]# pwd
   /root/atower-pgsql
   [root@bastion atower-pgsql]# ll
   total 379744
   -rw-r--r--. 1 root root     19966 Feb  9 05:14 ansible.cfg
   drwxr-xr-x. 7 root root       230 Feb  9 05:46 ansible-tower-setup-bundle-3.8.1-1
   -rw-r--r--. 1 root root 388792591 Feb  9 05:18 ansible-tower-setup-bundle-latest.el8.tar.gz
   -rw-r--r--. 1 root root      3747 Feb  9 05:14 check-pgsql-replication.yml
   -rw-r--r--. 1 root root       216 Feb  9 05:32 hosts
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
   [root@bastion atower-pgsql]# ansible-playbook -i hosts setup-pgsql-replication.yml
   ...
   PLAY RECAP ******************************************************************************************************************************************************************************************************************************
   db1                        : ok=8    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   db2                        : ok=12   changed=6    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
   tower1                     : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   tower2                     : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   tower3                     : ok=4    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
   
   [root@bastion atower-pgsql]#
   
   ```

5. postgresql replication 확인 방법 1

   ```shell
   [root@bastion atower-pgsql]# pwd
   /root/atower-pgsql
   [root@bastion atower-pgsql]#
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
       "msg": "Replication latency is 1.335519"
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

6. postgresql replication 확인 방법 2 (wal 확인)

   ```shell
   [root@bastion atower-pgsql]# ssh db1
   Activate the web console with: systemctl enable --now cockpit.socket
   
   This system is not registered to Red Hat Insights. See https://cloud.redhat.com/
   To register this system, run: insights-client --register
   
   Last login: Tue Feb  9 06:14:19 2021 from 192.168.1.30
   [root@db1 ~]#
   [root@db1 ~]#
   [root@db1 ~]# netstat -atnp
   Active Internet connections (servers and established)
   Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
   tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1/systemd
   tcp        0      0 192.168.122.1:53        0.0.0.0:*               LISTEN      1762/dnsmasq
   tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1129/sshd
   tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      1125/cupsd
   tcp        0      0 0.0.0.0:5432            0.0.0.0:*               LISTEN      36428/postmaster
   tcp        0      0 192.168.1.34:5432       192.168.1.31:56526      ESTABLISHED 39456/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.33:49948      ESTABLISHED 36462/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.32:43180      ESTABLISHED 39442/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.31:56474      ESTABLISHED 39354/postgres: awx
   tcp        0      0 192.168.1.34:22         192.168.1.30:33556      ESTABLISHED 39460/sshd: root [p
   tcp        0      0 192.168.1.34:5432       192.168.1.33:50758      ESTABLISHED 39448/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.32:43172      ESTABLISHED 39438/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.33:49946      ESTABLISHED 36456/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.33:50726      ESTABLISHED 39394/postgres: awx
   tcp        0      0 192.168.0.34:5432       192.168.0.35:34956      ESTABLISHED 38665/postgres: wal
   tcp        0      0 192.168.1.34:5432       192.168.1.32:42366      ESTABLISHED 36463/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.31:55718      ESTABLISHED 36464/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.33:49944      ESTABLISHED 36455/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.32:43176      ESTABLISHED 39440/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.32:42364      ESTABLISHED 36458/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.33:50764      ESTABLISHED 39451/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.31:56532      ESTABLISHED 39459/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.31:56530      ESTABLISHED 39458/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.31:55714      ESTABLISHED 36460/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.33:50762      ESTABLISHED 39450/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.32:43182      ESTABLISHED 39443/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.32:42362      ESTABLISHED 36457/postgres: awx
   tcp        0      0 192.168.1.34:5432       192.168.1.31:55716      ESTABLISHED 36461/postgres: awx
   tcp6       0      0 :::111                  :::*                    LISTEN      1/systemd
   tcp6       0      0 :::22                   :::*                    LISTEN      1129/sshd
   tcp6       0      0 ::1:631                 :::*                    LISTEN      1125/cupsd
   tcp6       0      0 :::5432                 :::*                    LISTEN      36428/postmaster
   [root@db1 ~]#
   [root@db1 ~]#
   [root@db1 ~]# exit
   logout
   Connection to db1 closed.
   [root@bastion atower-pgsql]# ssh db2
   Activate the web console with: systemctl enable --now cockpit.socket
   
   This system is not registered to Red Hat Insights. See https://cloud.redhat.com/
   To register this system, run: insights-client --register
   
   Last login: Tue Feb  9 06:14:17 2021 from 192.168.1.30
   [root@db2 ~]#
   [root@db2 ~]# netstat -atnp
   Active Internet connections (servers and established)
   Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
   tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      1/systemd
   tcp        0      0 192.168.122.1:53        0.0.0.0:*               LISTEN      1848/dnsmasq
   tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      1130/sshd
   tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      1127/cupsd
   tcp        0      0 0.0.0.0:5432            0.0.0.0:*               LISTEN      36685/postmaster
   tcp        0    196 192.168.1.35:22         192.168.1.30:51122      ESTABLISHED 37261/sshd: root [p
   tcp        0      0 192.168.0.35:34956      192.168.0.34:5432       ESTABLISHED 36691/postgres: wal
   tcp6       0      0 :::111                  :::*                    LISTEN      1/systemd
   tcp6       0      0 :::22                   :::*                    LISTEN      1130/sshd
   tcp6       0      0 ::1:631                 :::*                    LISTEN      1127/cupsd
   tcp6       0      0 :::5432                 :::*                    LISTEN      36685/postmaster
   [root@db2 ~]#
   ```

7. Tower 3개 모두 접속되는지 확인

   ![image-20210209063516688](C:\Users\jakim\Desktop\navercloud\pic\image-20210209063516688.png)

   ![image-20210209063532455](C:\Users\jakim\Desktop\navercloud\pic\image-20210209063532455.png)

   ![image-20210209063541805](C:\Users\jakim\Desktop\navercloud\pic\image-20210209063541805.png)

   