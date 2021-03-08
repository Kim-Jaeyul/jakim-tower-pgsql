## RHEL 7

### iso 마운트

```sh
[root@tower ~]# mkdir /mnt/disc1 /mnt/disc2
[root@tower ~]# mount -r -o loop /tmp/rhel-server-7.9-x86_64-dvd.iso /mnt/disc1/
[root@tower ~]# mount -r -o loop /tmp/rhscl-server-3.5-rhel-7-x86_64-dvd.iso /mnt/disc2/
```



### copy repo ISO 파일

```shell
[root@tower ~]# cp /mnt/disc1/media.repo /etc/yum.repos.d/rhel7disc.repo
[root@tower ~]# chmod 644 /etc/yum.repos.d/rhel7disc.repo
```



### local repo로 설정

```
[root@tower ~]# vi /etc/yum.repos.d/rhel7disc.repo

[RHEL7-InstallMedia]
name=Red Hat Enterprise Linux 7.9 (DVD)
mediaid=1600369739.509793
metadata_expire=-1
gpgcheck=1
cost=500
baseurl=file:///mnt/disc1/
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release

[RHSCL]
name=Red Hat Software Collections 3.5 for Red Hat Enterprise Linux 7 (DVD)
mediaid=1589893602.490144
metadata_expire=-1
gpgcheck=1
cost=500
baseurl=file:///mnt/disc2/
enabled=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-redhat-release
```



### repo 확인하기

```
[root@tower ~]# yum repolist
Loaded plugins: product-id, search-disabled-repos, subscription-manager
RHEL7-InstallMedia | 2.8 kB 00:00
RHSCL | 3.5 kB 00:00
(1/3): RHEL7-InstallMedia/group | 628 kB 00:00
(2/3): RHEL7-InstallMedia/primary | 2.1 MB 00:00
(3/3): RHSCL/primary_db | 1.3 MB 00:00
RHEL7-InstallMedia 5230/5230
repo id repo name status
RHEL7-InstallMedia Red Hat Enterprise Linux 7.9 (DVD) 5,230
RHSCL Red Hat Software Collections 3.5 for Red Hat Enterpris 2,724
repolist: 7,954
```



### rsync 설치

```
[root@tower ~]# yum install rsync
```



### tower 번들 설치

```
[root@tower ~]# ./setup.sh
```



### iso umount(optional)

```
[root@tower ~]# rm /etc/yum.repos.d/rhel7disc.repo
rm: remove regular file ‘/etc/yum.repos.d/rhel7disc.repo’? y

[root@tower ~]# umount /mnt/disc1 /mnt/disc2
[root@tower ~]# rm -r /mnt/disc1/ /mnt/disc2/
rm: remove directory ‘/mnt/disc1/’? y
rm: remove directory ‘/mnt/disc2/’? y
```

