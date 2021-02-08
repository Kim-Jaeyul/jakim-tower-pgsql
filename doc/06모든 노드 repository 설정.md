# 모든 노드 repository 설정

1. repository copy

   ```shell
   [root@bastion ~]# scp /etc/yum.repos.d/BaseOS.repo tower1:/etc/yum.repos.d/
   BaseOS.repo                                                                                                                                                                                            100%  296    93.6KB/s   00:00
   [root@bastion ~]# scp /etc/yum.repos.d/BaseOS.repo tower2:/etc/yum.repos.d/
   BaseOS.repo                                                                                                                                                                                            100%  296    44.4KB/s   00:00
   [root@bastion ~]# scp /etc/yum.repos.d/BaseOS.repo tower3:/etc/yum.repos.d/
   BaseOS.repo                                                                                                                                                                                            100%  296   109.1KB/s   00:00
   [root@bastion ~]# scp /etc/yum.repos.d/BaseOS.repo db1:/etc/yum.repos.d/
   BaseOS.repo                                                                                                                                                                                            100%  296    48.2KB/s   00:00
   [root@bastion ~]# scp /etc/yum.repos.d/BaseOS.repo db2:/etc/yum.repos.d/
   BaseOS.repo                                                                                                                                                                                            100%  296    51.4KB/s   00:00
   [root@bastion ~]#
   ```

