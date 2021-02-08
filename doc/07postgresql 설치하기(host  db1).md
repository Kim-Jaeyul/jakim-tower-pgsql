# postgresql 설치하기(host : db1)

1. postgresql 설치

   ```shell
   [root@bastion ~]# ssh db1
   Activate the web console with: systemctl enable --now cockpit.socket
   
   This system is not registered to Red Hat Insights. See https://cloud.redhat.com/
   To register this system, run: insights-client --register
   
   Last login: Tue Feb  9 01:54:02 2021
   [root@db1 ~]#
   [root@db1 ~]# yum module list | grep postgresql
   postgresql           9.6          client, server [d]                       PostgreSQL server and client module   
   postgresql           10 [d]       client, server [d]                       PostgreSQL server and client module   
   postgresql           12           client, server [d]                       PostgreSQL server and client module   
   [root@db1 ~]#
   [root@db1 ~]# dnf module install -y postgresql
   ...
   Installed:
     libpq-12.4-1.el8_2.x86_64
     postgresql-10.14-1.module+el8.2.0+7801+be0fed80.x86_64
     postgresql-server-10.14-1.module+el8.2.0+7801+be0fed80.x86_64
   
   Complete!
   [root@db1 ~]#
   [root@db1 ~]# rpm -qa |grep postgresql
   postgresql-10.14-1.module+el8.2.0+7801+be0fed80.x86_64
   postgresql-server-10.14-1.module+el8.2.0+7801+be0fed80.x86_64
   [root@db1 ~]#
   ```

2. postgresql 설정

   ```shell
   [root@db1 ~]# postgresql-setup --initdb
    * Initializing database in '/var/lib/pgsql/data'
    * Initialized, logs are in /var/lib/pgsql/initdb_postgresql.log
   [root@db1 ~]#
   [root@db1 ~]# firewall-cmd --add-port=5432/tcp --perm
   success
   [root@db1 ~]# firewall-cmd --reload
   success
   [root@db1 ~]#
   ```

3. postgresql 서비스 시작

   ```shell
   [root@db1 /]# systemctl status postgresql.service
   ● postgresql.service - PostgreSQL database server
      Loaded: loaded (/usr/lib/systemd/system/postgresql.service; disabled; vendor preset: disabled)
      Active: inactive (dead)
   [root@db1 /]#
   [root@db1 /]#
   [root@db1 /]# systemctl enable postgresql.service
   Created symlink /etc/systemd/system/multi-user.target.wants/postgresql.service → /usr/lib/systemd/system/postgresql.service.
   [root@db1 /]# systemctl start postgresql.service
   [root@db1 /]# systemctl status postgresql.service
   ● postgresql.service - PostgreSQL database server
      Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
      Active: active (running) since Tue 2021-02-09 04:22:19 KST; 2s ago
     Process: 33950 ExecStartPre=/usr/libexec/postgresql-check-db-dir postgresql (code=exited, status=0/SUCCESS)
    Main PID: 33953 (postmaster)
       Tasks: 8 (limit: 49295)
      Memory: 16.8M
      CGroup: /system.slice/postgresql.service
              ├─33953 /usr/bin/postmaster -D /var/lib/pgsql/data
              ├─33954 postgres: logger process
              ├─33956 postgres: checkpointer process
              ├─33957 postgres: writer process
              ├─33958 postgres: wal writer process
              ├─33959 postgres: autovacuum launcher process
              ├─33960 postgres: stats collector process
              └─33961 postgres: bgworker: logical replication launcher
   
   Feb 09 04:22:19 db1.example.com systemd[1]: Starting PostgreSQL database server...
   Feb 09 04:22:19 db1.example.com postmaster[33953]: 2021-02-09 04:22:19.947 KST [33953] LOG:  listening on IPv6 a>
   Feb 09 04:22:19 db1.example.com postmaster[33953]: 2021-02-09 04:22:19.947 KST [33953] LOG:  listening on IPv4 a>
   Feb 09 04:22:19 db1.example.com postmaster[33953]: 2021-02-09 04:22:19.952 KST [33953] LOG:  listening on Unix s>
   Feb 09 04:22:19 db1.example.com postmaster[33953]: 2021-02-09 04:22:19.962 KST [33953] LOG:  listening on Unix s>
   Feb 09 04:22:19 db1.example.com postmaster[33953]: 2021-02-09 04:22:19.979 KST [33953] LOG:  redirecting log out>
   Feb 09 04:22:19 db1.example.com postmaster[33953]: 2021-02-09 04:22:19.979 KST [33953] HINT:  Future log output >
   Feb 09 04:22:19 db1.example.com systemd[1]: Started PostgreSQL database server.
   [root@db1 /]#
   ```

4. Ansible Tower에 필요한 User 생성

   ```shell
   [root@db1 ~]# su - postgres
   [postgres@db1 ~]$ pwd
   /var/lib/pgsql
   [postgres@db1 ~]$
   [postgres@db1 ~]$
   [postgres@db1 ~]$ ls -la
   total 16
   drwx------.  5 postgres postgres   97 Feb  9 04:19 .
   drwxr-xr-x. 61 root     root     4096 Feb  9 04:12 ..
   drwx------.  2 postgres postgres    6 Aug 25 23:20 backups
   -rw-r--r--.  1 postgres postgres   85 Aug 25 23:20 .bash_profile
   drwx------.  2 postgres postgres    6 Feb  9 04:19 .cache
   drwx------. 21 postgres postgres 4096 Feb  9 04:22 data
   -rw-------.  1 postgres postgres  901 Feb  9 04:19 initdb_postgresql.log
   [postgres@db1 ~]$
   [postgres@db1 ~]$ psql
   psql (10.14)
   Type "help" for help.
   
   postgres=# CREATE ROLE awxdbuser1 WITH LOGIN encrypted PASSWORD 'redhat123' VALID UNTIL 'infinity';
   CREATE ROLE
   postgres=#
   postgres=# CREATE USER replica REPLICATION LOGIN ENCRYPTED PASSWORD 'redhat123';
   CREATE ROLE
   postgres=#
   postgres=# \du
                                       List of roles
    Role name  |                         Attributes                         | Member of
   ------------+------------------------------------------------------------+-----------
    awxdbuser1 | Password valid until infinity                              | {}
    postgres   | Superuser, Create role, Create DB, Replication, Bypass RLS | {}
    replica    | Replication                                                | {}
   
   postgres=#
   postgres=# CREATE DATABASE tower1 WITH ENCODING='UTF8' OWNER=awxdbuser1 CONNECTION LIMIT=-1;
   CREATE DATABASE
   postgres=#
   postgres=# \l
                                      List of databases
      Name    |   Owner    | Encoding |   Collate   |    Ctype    |   Access privileges
   -----------+------------+----------+-------------+-------------+-----------------------
    postgres  | postgres   | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
    template0 | postgres   | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
              |            |          |             |             | postgres=CTc/postgres
    template1 | postgres   | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
              |            |          |             |             | postgres=CTc/postgres
    tower1    | awxdbuser1 | UTF8     | en_US.UTF-8 | en_US.UTF-8 |
   (4 rows)
   
   postgres=#
   postgres=# \q
   [postgres@db1 ~]$
   ```

5. postgresql 의 listening IP와 postgresql Client Authentication 변경

   ```shell
   [postgres@db1 ~]$ grep listen_addresses /var/lib/pgsql/data/postgresql.conf
   #listen_addresses = 'localhost'         # what IP address(es) to listen on;
   [postgres@db1 ~]$
   [postgres@db1 ~]$ vim /var/lib/pgsql/data/postgresql.conf
   [postgres@db1 ~]$
   [postgres@db1 ~]$ grep listen_addresses /var/lib/pgsql/data/postgresql.conf
   listen_addresses = '*'          # what IP address(es) to listen on;
   #listen_addresses = 'localhost'         # what IP address(es) to listen on;
   [postgres@db1 ~]$
   [postgres@db1 ~]$ vim /var/lib/pgsql/data/pg_hba.conf
   ...
   # TYPE  DATABASE        USER            ADDRESS                 METHOD
   
   # "local" is for Unix domain socket connections only
   local   all             all                                     peer
   # IPv4 local connections:
   host    all             all             127.0.0.1/32            ident
   host    all             awxdbuser1      192.168.1.31/32         md5
   host    all             awxdbuser1      192.168.1.32/32         md5
   host    all             awxdbuser1      192.168.1.33/32         md5
   host    all             awxdbuser1      192.168.1.34/32         md5
   host    all             awxdbuser1      192.168.1.35/32         md5
   # IPv6 local connections:
   host    all             all             ::1/128                 ident
   # Allow replication connections from localhost, by a user with the
   # replication privilege.
   local   replication     all                                     peer
   host    replication     all             127.0.0.1/32            ident
   host    replication     all             ::1/128                 ident
   host    replication     replica         192.168.1.34/32         trust
   host    replication     replica         192.168.1.35/32         trust
   [postgres@db1 ~]$
   [postgres@db1 ~]$ exit
   logout
   [root@db1 ~]#
   [root@db1 ~]#
   [root@db1 ~]# systemctl status postgresql.service
   ● postgresql.service - PostgreSQL database server
      Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
      Active: active (running) since Tue 2021-02-09 04:22:19 KST; 38min ago
     Process: 33950 ExecStartPre=/usr/libexec/postgresql-check-db-dir postgresql (code=exited, status=0/SUCCESS)
    Main PID: 33953 (postmaster)
       Tasks: 8 (limit: 49295)
      Memory: 26.9M
      CGroup: /system.slice/postgresql.service
              ├─33953 /usr/bin/postmaster -D /var/lib/pgsql/data
              ├─33954 postgres: logger process
              ├─33956 postgres: checkpointer process
              ├─33957 postgres: writer process
              ├─33958 postgres: wal writer process
              ├─33959 postgres: autovacuum launcher process
              ├─33960 postgres: stats collector process
              └─33961 postgres: bgworker: logical replication launcher
   
   Feb 09 04:22:19 db1.example.com systemd[1]: Starting PostgreSQL database server...
   Feb 09 04:22:19 db1.example.com postmaster[33953]: 2021-02-09 04:22:19.947 KST [33953] LOG:  listening on IPv6 a>
   Feb 09 04:22:19 db1.example.com postmaster[33953]: 2021-02-09 04:22:19.947 KST [33953] LOG:  listening on IPv4 a>
   Feb 09 04:22:19 db1.example.com postmaster[33953]: 2021-02-09 04:22:19.952 KST [33953] LOG:  listening on Unix s>
   Feb 09 04:22:19 db1.example.com postmaster[33953]: 2021-02-09 04:22:19.962 KST [33953] LOG:  listening on Unix s>
   Feb 09 04:22:19 db1.example.com postmaster[33953]: 2021-02-09 04:22:19.979 KST [33953] LOG:  redirecting log out>
   Feb 09 04:22:19 db1.example.com postmaster[33953]: 2021-02-09 04:22:19.979 KST [33953] HINT:  Future log output >
   Feb 09 04:22:19 db1.example.com systemd[1]: Started PostgreSQL database server.
   [root@db1 ~]#
   [root@db1 ~]#
   [root@db1 ~]# systemctl restart postgresql.service
   [root@db1 ~]#
   [root@db1 ~]# systemctl status postgresql.service
   ● postgresql.service - PostgreSQL database server
      Loaded: loaded (/usr/lib/systemd/system/postgresql.service; enabled; vendor preset: disabled)
      Active: active (running) (thawing) since Tue 2021-02-09 05:01:31 KST; 2s ago
     Process: 34444 ExecStartPre=/usr/libexec/postgresql-check-db-dir postgresql (code=exited, status=0/SUCCESS)
    Main PID: 34448 (postmaster)
       Tasks: 8 (limit: 49295)
      Memory: 17.0M
      CGroup: /system.slice/postgresql.service
              ├─34448 /usr/bin/postmaster -D /var/lib/pgsql/data
              ├─34449 postgres: logger process
              ├─34451 postgres: checkpointer process
              ├─34452 postgres: writer process
              ├─34453 postgres: wal writer process
              ├─34454 postgres: autovacuum launcher process
              ├─34455 postgres: stats collector process
              └─34456 postgres: bgworker: logical replication launcher
   
   Feb 09 05:01:31 db1.example.com systemd[1]: Starting PostgreSQL database server...
   Feb 09 05:01:31 db1.example.com postmaster[34448]: 2021-02-09 05:01:31.348 KST [34448] LOG:  listening on IPv4 a>
   Feb 09 05:01:31 db1.example.com postmaster[34448]: 2021-02-09 05:01:31.348 KST [34448] LOG:  listening on IPv6 a>
   Feb 09 05:01:31 db1.example.com postmaster[34448]: 2021-02-09 05:01:31.352 KST [34448] LOG:  listening on Unix s>
   Feb 09 05:01:31 db1.example.com postmaster[34448]: 2021-02-09 05:01:31.360 KST [34448] LOG:  listening on Unix s>
   Feb 09 05:01:31 db1.example.com postmaster[34448]: 2021-02-09 05:01:31.378 KST [34448] LOG:  redirecting log out>
   Feb 09 05:01:31 db1.example.com postmaster[34448]: 2021-02-09 05:01:31.378 KST [34448] HINT:  Future log output >
   Feb 09 05:01:31 db1.example.com systemd[1]: Started PostgreSQL database server.
   [root@db1 ~]#
   ```

