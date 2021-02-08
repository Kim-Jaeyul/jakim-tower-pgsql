# Bastion에 ansible 설치

1. 설치파일 압축해제

   ```shell
   [root@bastion ~]# tar xzf ansible-tower-setup-bundle-latest.el8.tar.gz
   [root@bastion ~]# cd ansible-tower-setup-bundle-3.8.1-1/
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# ll
   total 60
   -rw-r--r--.  1 root root   626 Jan 13 20:43 backup.yml
   drwxr-xr-x.  4 root root    28 Jan 13 20:44 bundle
   drwxr-xr-x.  3 root root    33 Jan 13 20:44 collections
   drwxr-xr-x.  2 root root    17 Jan 13 20:43 group_vars
   -rw-r--r--.  1 root root  8521 Jan 13 20:43 install.yml
   -rw-r--r--.  1 root root  2915 Jan 13 20:43 inventory
   drwxr-xr-x.  3 root root  8192 Jan 13 20:43 licenses
   -rw-r--r--.  1 root root  2506 Jan 13 20:43 README.md
   -rw-r--r--.  1 root root  1335 Jan 13 20:43 rekey.yml
   -rw-r--r--.  1 root root  3492 Jan 13 20:43 restore.yml
   drwxr-xr-x. 21 root root  4096 Jan 13 20:43 roles
   -rwxr-xr-x.  1 root root 10819 Jan 13 20:43 setup.sh
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   ```

2. inventory file 수정

   ```shell
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# cat inventory | grep -e admin_password -e pg_password | grep -v automationhub
   admin_password='Redhat1!'
   pg_password='Redhat1!'
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   ```

3. 설치 수행

   ```shell
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# curl google.com
   curl: (6) Could not resolve host: google.com
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# ll
   total 60
   -rw-r--r--.  1 root root   626 Jan 13 20:43 backup.yml
   drwxr-xr-x.  4 root root    28 Jan 13 20:44 bundle
   drwxr-xr-x.  3 root root    33 Jan 13 20:44 collections
   drwxr-xr-x.  2 root root    17 Jan 13 20:43 group_vars
   -rw-r--r--.  1 root root  8521 Jan 13 20:43 install.yml
   -rw-r--r--.  1 root root  2931 Feb  9 03:15 inventory
   drwxr-xr-x.  3 root root  8192 Jan 13 20:43 licenses
   -rw-r--r--.  1 root root  2506 Jan 13 20:43 README.md
   -rw-r--r--.  1 root root  1335 Jan 13 20:43 rekey.yml
   -rw-r--r--.  1 root root  3492 Jan 13 20:43 restore.yml
   drwxr-xr-x. 21 root root  4096 Jan 13 20:43 roles
   -rwxr-xr-x.  1 root root 10819 Jan 13 20:43 setup.sh
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]# ./setup.sh
   ...
   
   PLAY RECAP ******************************************************************************************************************************************************************************************************************************
   localhost                  : ok=174  changed=38   unreachable=0    failed=0    skipped=86   rescued=0    ignored=0
   
   The setup process completed successfully.
   Setup log saved to /var/log/tower/setup-2021-02-09-03:37:05.log.
   [root@bastion ansible-tower-setup-bundle-3.8.1-1]#
   ```

