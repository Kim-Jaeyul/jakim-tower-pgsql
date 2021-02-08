# Bastion에 dnsmasq 설정(optional)

1. 방화벽 open

   ```shell
   [root@bastion ~]# firewall-cmd --add-service=dns --perm
   success
   [root@bastion ~]# firewall-cmd --reload
   success
   [root@bastion ~]# firewall-cmd --list-all
   public (active)
     target: default
     icmp-block-inversion: no
     interfaces: enp1s0 enp2s0
     sources:
     services: cockpit dhcpv6-client dns http ssh
     ports: 80/tcp 443/tcp 8080/tcp
     protocols:
     masquerade: no
     forward-ports:
     source-ports:
     icmp-blocks:
     rich rules:
   [root@bastion ~]#
   ```

2. /etc/dnsmasq.conf 설정

   ```shell
   [root@bastion ~]# cat /etc/dnsmasq.conf |grep -v "#"
   listen-address=::1,127.0.0.1,192.168.1.30
   interface=enp2s0
   except-interface=virbr0
   no-dhcp-interface=virbr0
   bind-interfaces
   
   address=/example.com/127.0.0.1
   address=/example.com/192.168.1.30
   
   expand-hosts
   domain=example.com
   
   conf-dir=/etc/dnsmasq.d,.rpmnew,.rpmsave,.rpmorig
   [root@bastion ~]#
   [root@bastion ~]# ip a
   1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
       link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
       inet 127.0.0.1/8 scope host lo
          valid_lft forever preferred_lft forever
       inet6 ::1/128 scope host
          valid_lft forever preferred_lft forever
   2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
       link/ether 56:6f:c9:f7:00:02 brd ff:ff:ff:ff:ff:ff
       inet 192.168.0.30/24 brd 192.168.0.255 scope global noprefixroute enp1s0
          valid_lft forever preferred_lft forever
       inet6 fe80::546f:c9ff:fef7:2/64 scope link
          valid_lft forever preferred_lft forever
   3: enp2s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
       link/ether 56:6f:c9:f7:00:03 brd ff:ff:ff:ff:ff:ff
       inet 192.168.1.30/24 brd 192.168.1.255 scope global noprefixroute enp2s0
          valid_lft forever preferred_lft forever
       inet6 fe80::546f:c9ff:fef7:3/64 scope link
          valid_lft forever preferred_lft forever
   4: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
       link/ether 52:54:00:12:81:2d brd ff:ff:ff:ff:ff:ff
       inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
          valid_lft forever preferred_lft forever
   5: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc fq_codel master virbr0 state DOWN group default qlen 1000
       link/ether 52:54:00:12:81:2d brd ff:ff:ff:ff:ff:ff
   [root@bastion ~]#
   [root@bastion ~]# dnsmasq --test
   dnsmasq: syntax check OK.
   [root@bastion ~]#
   ```

3. /etc/hosts 에 추가

   ```
   [root@bastion ~]# cat /etc/hosts
   127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
   ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
   
   192.168.1.30 bastion.example.com bastion
   192.168.1.31 tower1.example.com tower1
   192.168.1.32 tower2.example.com tower2
   192.168.1.33 tower3.example.com tower3
   192.168.1.34 db1.example.com db1
   192.168.1.35 db2.example.com db2
   [root@bastion ~]#
   ```

4. dnsmasq 실행

   ```shell
   [root@bastion ~]# systemctl start dnsmasq
   [root@bastion ~]# systemctl enable dnsmasq
   Created symlink /etc/systemd/system/multi-user.target.wants/dnsmasq.service → /usr/lib/systemd/system/dnsmasq.service.
   [root@bastion ~]# systemctl status dnsmasq
   ● dnsmasq.service - DNS caching server.
      Loaded: loaded (/usr/lib/systemd/system/dnsmasq.service; enabled; vendor preset: disabled)
      Active: active (running) since Tue 2021-02-09 03:54:44 KST; 14s ago
    Main PID: 46965 (dnsmasq)
       Tasks: 1 (limit: 49296)
      Memory: 812.0K
      CGroup: /system.slice/dnsmasq.service
              └─46965 /usr/sbin/dnsmasq -k
   
   Feb 09 03:54:44 bastion.example.com systemd[1]: Started DNS caching server..
   Feb 09 03:54:44 bastion.example.com dnsmasq[46965]: listening on enp2s0(#3): 192.168.1.30
   Feb 09 03:54:44 bastion.example.com dnsmasq[46965]: listening on lo(#1): 127.0.0.1
   Feb 09 03:54:44 bastion.example.com dnsmasq[46965]: listening on enp2s0(#3): fe80::546f:c9ff:fef7:3%enp2s0
   Feb 09 03:54:44 bastion.example.com dnsmasq[46965]: listening on lo(#1): ::1
   Feb 09 03:54:44 bastion.example.com dnsmasq[46965]: started, version 2.79 cachesize 150
   Feb 09 03:54:44 bastion.example.com dnsmasq[46965]: compile time options: IPv6 GNU-getopt DBus no-i18n IDN2 DHCP DHCPv6 no-Lua TFTP no-conntrack ipset auth DNSSEC loop-detect inotify
   Feb 09 03:54:44 bastion.example.com dnsmasq[46965]: no servers found in /etc/resolv.conf, will retry
   Feb 09 03:54:44 bastion.example.com dnsmasq[46965]: read /etc/hosts - 8 addresses
   [root@bastion ~]#
   ```

   