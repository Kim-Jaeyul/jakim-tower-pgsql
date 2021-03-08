### haproxy 설정

```sh
[root@towerha ~]# cat /etc/haproxy/haproxy.cfg
#---------------------------------------------------------------------
# Example configuration for a possible web application.  See the
# full configuration options online.
#
#   https://www.haproxy.org/download/1.8/doc/configuration.txt
#
#---------------------------------------------------------------------

#---------------------------------------------------------------------
# Global settings
#---------------------------------------------------------------------
global
    # to have these messages end up in /var/log/haproxy.log you will
    # need to:
    #
    # 1) configure syslog to accept network log events.  This is done
    #    by adding the '-r' option to the SYSLOGD_OPTIONS in
    #    /etc/sysconfig/syslog
    #
    # 2) configure local2 events to go to the /var/log/haproxy.log
    #   file. A line like the following can be added to
    #   /etc/sysconfig/syslog
    #
    #    local2.*                       /var/log/haproxy.log
    #
    log         127.0.0.1 local2

    chroot      /var/lib/haproxy
    pidfile     /var/run/haproxy.pid
    maxconn     4000
    user        haproxy
    group       haproxy
    daemon

    # turn on stats unix socket
    stats socket /var/lib/haproxy/stats

    # utilize system-wide crypto-policies
    ssl-default-bind-ciphers PROFILE=SYSTEM
    ssl-default-server-ciphers PROFILE=SYSTEM

#---------------------------------------------------------------------
# common defaults that all the 'listen' and 'backend' sections will
# use if not designated in their block
#---------------------------------------------------------------------
defaults
    mode                    http
    log                     global
    option                  httplog
    option                  dontlognull
    option http-server-close
    option forwardfor       except 127.0.0.0/8
    option                  redispatch
    retries                 3
    timeout http-request    10s
    timeout queue           1m
    timeout connect         10s
    timeout client          1m
    timeout server          1m
    timeout http-keep-alive 10s
    timeout check           10s
    maxconn                 4000

#---------------------------------------------------------------------
# static backend for serving up images, stylesheets and such
#---------------------------------------------------------------------
backend static
    balance     roundrobin
    server      static 127.0.0.1:4441 check

#---------------------------------------------------------------------
# round robin balancing between the various backends
#---------------------------------------------------------------------
frontend tower-http
    bind *:80
    default_backend tower-http
    mode tcp
    option tcplog

backend tower-http
    balance source
    mode tcp
    server tower1 192.168.0.31:80 check
    server tower2 192.168.0.32:80 check
    server tower3 192.168.0.33:80 check
[root@towerha ~]# 
```



### 방화벽 설정

```
[root@towerha ~]# firewall-cmd --add-service=http --perm
success
[root@towerha ~]# firewall-cmd --add-service=https --perms
success
[root@towerha ~]# 
[root@towerha ~]# firewall-cmd --reload
success
[root@towerha ~]# 
[root@towerha ~]# firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp1s0
  sources: 
  services: cockpit dhcpv6-client http https ssh
  ports: 80/tcp
  protocols: 
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
[root@towerha ~]# 
```



### service start

```
[root@towerha ~]# systemctl enable haproxy
Created symlink /etc/systemd/system/multi-user.target.wants/haproxy.service  /usr/lib/systemd/system/haproxy.service.
[root@towerha ~]# 
[root@towerha ~]# systemctl start haproxy

 haproxy.service - HAProxy Load Balancer
   Loaded: loaded (/usr/lib/systemd/system/haproxy.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2020-12-08 23:56:44 EST; 2s ago
  Process: 33160 ExecStartPre=/usr/sbin/haproxy -f $CONFIG -c -q $OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 33162 (haproxy)
    Tasks: 2 (limit: 23512)
   Memory: 2.9M
   CGroup: /system.slice/haproxy.service
           33162 /usr/sbin/haproxy -Ws -f /etc/haproxy/haproxy.cfg -p /run/haproxy.pid
           33163 /usr/sbin/haproxy -Ws -f /etc/haproxy/haproxy.cfg -p /run/haproxy.pid

Dec 08 23:56:44 towerha.example.com systemd[1]: Starting HAProxy Load Balancer...
Dec 08 23:56:44 towerha.example.com haproxy[33162]: [WARNING] 342/235644 (33162) : config : 'option forwardfor' ignored for frontend 'tower-http' as it requires HTTP mode.
Dec 08 23:56:44 towerha.example.com haproxy[33162]: [WARNING] 342/235644 (33162) : config : 'option forwardfor' ignored for backend 'tower-http' as it requires HTTP mode.
Dec 08 23:56:44 towerha.example.com systemd[1]: Started HAProxy Load Balancer.

[root@towerha ~]# 
[root@towerha ~]# systemctl status haproxy

 haproxy.service - HAProxy Load Balancer
   Loaded: loaded (/usr/lib/systemd/system/haproxy.service; enabled; vendor preset: disabled)
   Active: active (running) since Tue 2020-12-08 23:56:44 EST; 5s ago
  Process: 33160 ExecStartPre=/usr/sbin/haproxy -f $CONFIG -c -q $OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 33162 (haproxy)
    Tasks: 2 (limit: 23512)
   Memory: 2.9M
   CGroup: /system.slice/haproxy.service
           33162 /usr/sbin/haproxy -Ws -f /etc/haproxy/haproxy.cfg -p /run/haproxy.pid
           33163 /usr/sbin/haproxy -Ws -f /etc/haproxy/haproxy.cfg -p /run/haproxy.pid

Dec 08 23:56:44 towerha.example.com systemd[1]: Starting HAProxy Load Balancer...
Dec 08 23:56:44 towerha.example.com haproxy[33162]: [WARNING] 342/235644 (33162) : config : 'option forwardfor' ignored for frontend 'tower-http' as it requires HTTP mode.
Dec 08 23:56:44 towerha.example.com haproxy[33162]: [WARNING] 342/235644 (33162) : config : 'option forwardfor' ignored for backend 'tower-http' as it requires HTTP mode.
Dec 08 23:56:44 towerha.example.com systemd[1]: Started HAProxy Load Balancer.

[root@towerha ~]# 
```

