# postgresql 설치하기(host : db2)

1. postgresql 설치

   ```shell
   [root@db1 ~]# exit
   logout
   Connection to db1 closed.
   [root@bastion ~]#
   [root@bastion ~]# ssh db2
   Activate the web console with: systemctl enable --now cockpit.socket
   
   This system is not registered to Red Hat Insights. See https://cloud.redhat.com/
   To register this system, run: insights-client --register
   
   Last login: Tue Feb  9 01:58:16 2021
   [root@db2 ~]#
   [root@db2 ~]# dnf module list |grep postgresql
   postgresql           9.6          client, server [d]                       PostgreSQL server and client module   
   postgresql           10 [d]       client, server [d]                       PostgreSQL server and client module   
   postgresql           12           client, server [d]                       PostgreSQL server and client module   
   [root@db2 ~]#
   [root@db2 ~]# dnf module install -y postgresql
   ...
   Installed:
     libpq-12.4-1.el8_2.x86_64
     postgresql-10.14-1.module+el8.2.0+7801+be0fed80.x86_64
     postgresql-server-10.14-1.module+el8.2.0+7801+be0fed80.x86_64
   
   Complete!
   [root@db2 ~]#
   [root@db2 ~]# rpm -qa|grep postgresql
   postgresql-10.14-1.module+el8.2.0+7801+be0fed80.x86_64
   postgresql-server-10.14-1.module+el8.2.0+7801+be0fed80.x86_64
   [root@db2 ~]#
   [root@db2 ~]# firewall-cmd --add-port=5432/tcp --permanent
   success
   [root@db2 ~]# firewall-cmd --reload
   success
   [root@db2 ~]# firewall-cmd --list-all
   public (active)
     target: default
     icmp-block-inversion: no
     interfaces: enp1s0 enp2s0
     sources:
     services: cockpit dhcpv6-client ssh
     ports: 5432/tcp
     protocols:
     masquerade: no
     forward-ports:
     source-ports:
     icmp-blocks:
     rich rules:
   [root@db2 ~]#
   ```

