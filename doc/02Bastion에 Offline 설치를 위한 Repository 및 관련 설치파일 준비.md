# Bastion에 Offline 설치를 위한 Repository 및 관련 설치파일 준비

1. Ansible Tower Download

   ```sh
   [root@bastion ~]# wget https://releases.ansible.com/ansible-tower/setup-bundle/ansible-tower-setup-bundle-latest.el8.tar.gz
   --2021-02-09 01:40:42--  https://releases.ansible.com/ansible-tower/setup-bundle/ansible-tower-setup-bundle-latest.el8.tar.gz
   Resolving releases.ansible.com (releases.ansible.com)... 104.26.1.234, 172.67.68.251, 104.26.0.234, ...
   Connecting to releases.ansible.com (releases.ansible.com)|104.26.1.234|:443... connected.
   HTTP request sent, awaiting response... 200 OK
   Length: 388792591 (371M) [application/x-gzip]
   Saving to: ‘ansible-tower-setup-bundle-latest.el8.tar.gz’
   
   ansible-tower-setup-bundle-latest.el8.tar.gz        100%[=================================================================================================================>] 370.78M  9.43MB/s    in 40s
   
   2021-02-09 01:41:22 (9.36 MB/s) - ‘ansible-tower-setup-bundle-latest.el8.tar.gz’ saved [388792591/388792591]
   
   [root@bastion ~]#
   [root@bastion ~]# ls -lh
   total 9.2G
   -rw-------. 1 root root 1.3K Feb  9 01:14 anaconda-ks.cfg
   -rw-r--r--. 1 root root 371M Jan 13 20:46 ansible-tower-setup-bundle-latest.el8.tar.gz
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Desktop
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Documents
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Downloads
   -rw-r--r--. 1 root root 1.6K Feb  9 01:16 initial-setup-ks.cfg
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Music
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Pictures
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Public
   -rw-r--r--. 1 root root 8.9G Feb  9 01:54 rhel-8.3-x86_64-dvd.iso
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Templates
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Videos
   [root@bastion ~]#
   ```

   

2. RHEL Base ISO 준비

   ```shell
   [root@bastion ~]# ls -lh
   total 9.2G
   -rw-------. 1 root root 1.3K Feb  9 01:14 anaconda-ks.cfg
   -rw-r--r--. 1 root root 371M Jan 13 20:46 ansible-tower-setup-bundle-latest.el8.tar.gz
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Desktop
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Documents
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Downloads
   -rw-r--r--. 1 root root 1.6K Feb  9 01:16 initial-setup-ks.cfg
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Music
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Pictures
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Public
   -rw-r--r--. 1 root root 8.9G Feb  9 01:54 rhel-8.3-x86_64-dvd.iso
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Templates
   drwxr-xr-x. 2 root root    6 Feb  9 01:29 Videos
   [root@bastion ~]#
   ```



3. RHEL Base ISO mount

   ```shell
   [root@bastion ~]# mkdir /mnt/disc
   [root@bastion ~]# mount -r -o loop /root/rhel-8.3-x86_64-dvd.iso /mnt/disc/
   ```



4. 예제 repo file 복사

   ```shell
   [root@bastion ~]# cp /mnt/disc/media.repo /etc/yum.repos.d/rhel8disc.repo
   [root@bastion ~]# chmod 644 /etc/yum.repos.d/rhel8disc.repo
   ```



5. repo file 수정

   ```shell
   [root@bastion ~]# cat << EOF > /etc/yum.repos.d/rhel8disc.repo
   [RHEL8-Server-DVD-Appstream]
   name=Red Hat Enterprise Linux 8.3.0 (DVD) Appstream
   mediaid=None
   metadata_expire=-1
   gpgcheck=1
   cost=500
   baseurl=file:///mnt/disc/AppStream
   enabled=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
   
   [RHEL8-Server-DVD-BaseOS]
   name=Red Hat Enterprise Linux 8.3.0 (DVD) BaseOS
   mediaid=None
   metadata_expire=-1
   gpgcheck=1
   cost=500
   baseurl=file:///mnt/disc/BaseOS
   enabled=1
   gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
   EOF
   [root@bastion ~]#
   ```



6. repo 확인

   ```shell
   [root@bastion ~]# dnf repolist
   Updating Subscription Management repositories.
   Unable to read consumer identity
   
   This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
   
   repo id                                                                                                    repo name
   RHEL8-Server-DVD-Appstream                                                                                 Red Hat Enterprise Linux 8.3.0 (DVD) Appstream
   RHEL8-Server-DVD-BaseOS                                                                                    Red Hat Enterprise Linux 8.3.0 (DVD) BaseOS
   [root@bastion ~]#
   ```



7. httpd와 rsync 설치

   ```shell
   [root@bastion ~]# dnf install -y rsync httpd
   ...
   Complete!
   [root@bastion ~]#
   ```



8. umount DVD 후, http 경로로 다시 잡기

   ```shell
   [root@bastion ~]# mv /etc/yum.repos.d/rhel8disc.repo /etc/yum.repos.d/rhel8disc.repo.bak
   [root@bastion ~]#
   [root@bastion ~]# umount /mnt/disc
   [root@bastion ~]# rm -r /mnt/disc
   rm: remove directory '/mnt/disc'? y
   [root@bastion ~]#
   [root@bastion ~]# mkdir -p /var/www/html/cdrom/
   [root@bastion ~]#
   [root@bastion ~]# mount -o loop /root/rhel-8.3-x86_64-dvd.iso /media
   mount: /media: WARNING: device write-protected, mounted read-only.
   [root@bastion ~]#
   [root@bastion ~]# shopt -s dotglob
   [root@bastion ~]#
   [root@bastion ~]# cp -apvR /media/* /var/www/html/cdrom/
   ...
   [root@bastion ~]#
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# cat /etc/httpd/conf/httpd.conf | grep -e ServerAdmin -e ServerName -e DocumentRoot -e Listen |grep -v "#"
   Listen 8080
   ServerAdmin root@192.168.1.30
   ServerName 192.168.1.30
   DocumentRoot "/var/www/html"
   [root@bastion ~]#
   [root@bastion ~]# httpd -t
   Syntax OK
   [root@bastion ~]# service httpd start
   Redirecting to /bin/systemctl start httpd.service
   [root@bastion ~]#
   [root@bastion ~]# systemctl status httpd
   ● httpd.service - The Apache HTTP Server
      Loaded: loaded (/usr/lib/systemd/system/httpd.service; disabled; vendor preset: disabled)
      Active: active (running) since Tue 2021-02-09 02:50:11 KST; 9s ago
        Docs: man:httpd.service(8)
    Main PID: 32213 (httpd)
      Status: "Running, listening on: port 80"
       Tasks: 213 (limit: 49296)
      Memory: 30.2M
      CGroup: /system.slice/httpd.service
              ├─32213 /usr/sbin/httpd -DFOREGROUND
              ├─32214 /usr/sbin/httpd -DFOREGROUND
              ├─32215 /usr/sbin/httpd -DFOREGROUND
              ├─32216 /usr/sbin/httpd -DFOREGROUND
              └─32217 /usr/sbin/httpd -DFOREGROUND
   
   Feb 09 02:50:11 bastion.example.com systemd[1]: Starting The Apache HTTP Server...
   Feb 09 02:50:11 bastion.example.com systemd[1]: Started The Apache HTTP Server.
   Feb 09 02:50:11 bastion.example.com httpd[32213]: Server configured, listening on: port 80
   [root@bastion ~]# systemctl enable httpd
   Created symlink /etc/systemd/system/multi-user.target.wants/httpd.service → /usr/lib/systemd/system/httpd.service.
   [root@bastion ~]#
   [root@bastion ~]#
   [root@bastion ~]# systemctl status httpd
   ● httpd.service - The Apache HTTP Server
      Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
      Active: active (running) since Tue 2021-02-09 02:50:11 KST; 20s ago
        Docs: man:httpd.service(8)
    Main PID: 32213 (httpd)
      Status: "Running, listening on: port 80"
       Tasks: 213 (limit: 49296)
      Memory: 30.2M
      CGroup: /system.slice/httpd.service
              ├─32213 /usr/sbin/httpd -DFOREGROUND
              ├─32214 /usr/sbin/httpd -DFOREGROUND
              ├─32215 /usr/sbin/httpd -DFOREGROUND
              ├─32216 /usr/sbin/httpd -DFOREGROUND
              └─32217 /usr/sbin/httpd -DFOREGROUND
   
   Feb 09 02:50:11 bastion.example.com systemd[1]: Starting The Apache HTTP Server...
   Feb 09 02:50:11 bastion.example.com systemd[1]: Started The Apache HTTP Server.
   Feb 09 02:50:11 bastion.example.com httpd[32213]: Server configured, listening on: port 80
   [root@bastion ~]#
   [root@bastion ~]# ls -lZ /var/www/html/cdrom/
   total 56
   dr-xr-xr-x. 4 root root system_u:object_r:iso9660_t:s0    38 Oct  9 19:40 AppStream
   dr-xr-xr-x. 4 root root system_u:object_r:iso9660_t:s0    38 Oct  9 19:40 BaseOS
   dr-xr-xr-x. 3 root root system_u:object_r:iso9660_t:s0    18 Oct  9 19:40 EFI
   -r--r--r--. 1 root root system_u:object_r:iso9660_t:s0  8266 Oct  9 19:37 EULA
   -r--r--r--. 1 root root system_u:object_r:iso9660_t:s0  1455 Oct  9 19:37 extra_files.json
   -r--r--r--. 1 root root system_u:object_r:iso9660_t:s0 18092 Oct  9 19:37 GPL
   dr-xr-xr-x. 3 root root system_u:object_r:iso9660_t:s0    76 Oct  9 19:40 images
   dr-xr-xr-x. 2 root root system_u:object_r:iso9660_t:s0   256 Oct  9 19:40 isolinux
   -r--r--r--. 1 root root system_u:object_r:iso9660_t:s0   103 Oct  9 19:37 media.repo
   -r--r--r--. 1 root root system_u:object_r:iso9660_t:s0  1669 Oct  9 19:37 RPM-GPG-KEY-redhat-beta
   -r--r--r--. 1 root root system_u:object_r:iso9660_t:s0  5134 Oct  9 19:37 RPM-GPG-KEY-redhat-release
   -r--r--r--. 1 root root system_u:object_r:iso9660_t:s0  1796 Oct  9 19:40 TRANS.TBL
   [root@bastion ~]#
   [root@bastion ~]# restorecon -Rv /var/www/html
   ...
   [root@bastion ~]# ls -lZ /var/www/html/cdrom/
   total 56
   dr-xr-xr-x. 4 root root system_u:object_r:httpd_sys_content_t:s0    38 Oct  9 19:40 AppStream
   dr-xr-xr-x. 4 root root system_u:object_r:httpd_sys_content_t:s0    38 Oct  9 19:40 BaseOS
   dr-xr-xr-x. 3 root root system_u:object_r:httpd_sys_content_t:s0    18 Oct  9 19:40 EFI
   -r--r--r--. 1 root root system_u:object_r:httpd_sys_content_t:s0  8266 Oct  9 19:37 EULA
   -r--r--r--. 1 root root system_u:object_r:httpd_sys_content_t:s0  1455 Oct  9 19:37 extra_files.json
   -r--r--r--. 1 root root system_u:object_r:httpd_sys_content_t:s0 18092 Oct  9 19:37 GPL
   dr-xr-xr-x. 3 root root system_u:object_r:httpd_sys_content_t:s0    76 Oct  9 19:40 images
   dr-xr-xr-x. 2 root root system_u:object_r:httpd_sys_content_t:s0   256 Oct  9 19:40 isolinux
   -r--r--r--. 1 root root system_u:object_r:httpd_sys_content_t:s0   103 Oct  9 19:37 media.repo
   -r--r--r--. 1 root root system_u:object_r:httpd_sys_content_t:s0  1669 Oct  9 19:37 RPM-GPG-KEY-redhat-beta
   -r--r--r--. 1 root root system_u:object_r:httpd_sys_content_t:s0  5134 Oct  9 19:37 RPM-GPG-KEY-redhat-release
   -r--r--r--. 1 root root system_u:object_r:httpd_sys_content_t:s0  1796 Oct  9 19:40 TRANS.TBL
   [root@bastion ~]#
   [root@bastion ~]# cat << EOF > /etc/yum.repos.d/BaseOS.repo
   > [RHEL8-Server-DVD-Appstream]
   > name=Red Hat Enterprise Linux 8.3.0 (DVD) Appstream
   > enabled=1
   > gpgcheck=0
   > baseurl=http://192.168.1.30:8080/cdrom/AppStream/
   >
   > [RHEL8-Server-DVD-BaseOS]
   > name=Red Hat Enterprise Linux 8.3.0 (DVD) BaseOS
   > enabled=1
   > gpgcheck=0
   > baseurl=http://192.168.1.30:8080/cdrom/BaseOS/
   > EOF
   [root@bastion ~]#
   [root@bastion ~]# firewall-cmd --list-all
   public (active)
     target: default
     icmp-block-inversion: no
     interfaces: enp1s0 enp2s0
     sources:
     services: cockpit dhcpv6-client ssh
     ports:
     protocols:
     masquerade: no
     forward-ports:
     source-ports:
     icmp-blocks:
     rich rules:
   [root@bastion ~]# firewall-cmd --add-service=http --perm
   success
   [root@bastion ~]# firewall-cmd --reload
   success
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# firewall-cmd --add-port=8080/tcp --perm
   success
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# firewall-cmd --reload
   success
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# firewall-cmd --list-all
   public (active)
     target: default
     icmp-block-inversion: no
     interfaces: enp1s0 enp2s0
     sources:
     services: cockpit dhcpv6-client http ssh
     ports: 80/tcp 443/tcp 8080/tcp
     protocols:
     masquerade: no
     forward-ports:
     source-ports:
     icmp-blocks:
     rich rules:
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   [root@bastion ~]# systemctl restart httpd
   [root@bastion ~]# systemctl status httpd
   ● httpd.service - The Apache HTTP Server
      Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled; vendor preset: disabled)
      Active: active (running) (thawing) since Tue 2021-02-09 03:34:14 KST; 5s ago
        Docs: man:httpd.service(8)
    Main PID: 42530 (httpd)
      Status: "Started, listening on: port 8080"
       Tasks: 213 (limit: 49296)
      Memory: 31.9M
      CGroup: /system.slice/httpd.service
              ├─42530 /usr/sbin/httpd -DFOREGROUND
              ├─42533 /usr/sbin/httpd -DFOREGROUND
              ├─42534 /usr/sbin/httpd -DFOREGROUND
              ├─42535 /usr/sbin/httpd -DFOREGROUND
              └─42536 /usr/sbin/httpd -DFOREGROUND
   
   Feb 09 03:34:14 bastion.example.com systemd[1]: Starting The Apache HTTP Server...
   Feb 09 03:34:14 bastion.example.com systemd[1]: Started The Apache HTTP Server.
   Feb 09 03:34:14 bastion.example.com httpd[42530]: Server configured, listening on: port 8080
   [root@bastion ~]#
   [root@bastion ~]# dnf clean all
   Updating Subscription Management repositories.
   Unable to read consumer identity
   
   This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
   
   13 files removed
   [root@bastion ~]#
   [root@bastion ~]# rm -rf /var/cache/yum/*
   [root@bastion ~]# dnf makecache
   Updating Subscription Management repositories.
   Unable to read consumer identity
   
   This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
   
   Red Hat Enterprise Linux 8.3.0 (DVD) Appstream                                                                                                                                                           165 MB/s | 6.3 MB     00:00
   Red Hat Enterprise Linux 8.3.0 (DVD) BaseOS                                                                                                                                                              107 MB/s | 2.3 MB     00:00
   Last metadata expiration check: 0:00:01 ago on Tue 09 Feb 2021 03:02:29 AM KST.
   Metadata cache created.
   [root@bastion ~]# dnf repolist
   Updating Subscription Management repositories.
   Unable to read consumer identity
   
   This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
   
   repo id                                                                                                    repo name
   RHEL8-Server-DVD-Appstream                                                                                 Red Hat Enterprise Linux 8.3.0 (DVD) Appstream
   RHEL8-Server-DVD-BaseOS                                                                                    Red Hat Enterprise Linux 8.3.0 (DVD) BaseOS
   [root@bastion ~]#
   ```

   