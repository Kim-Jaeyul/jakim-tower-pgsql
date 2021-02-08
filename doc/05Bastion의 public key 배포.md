# Bastion의 public key 배포

1. key generate

   ```shell
   [root@bastion ~]# ssh-key
   ssh-keygen   ssh-keyscan
   [root@bastion ~]# ssh-keygen
   Generating public/private rsa key pair.
   Enter file in which to save the key (/root/.ssh/id_rsa):
   Created directory '/root/.ssh'.
   Enter passphrase (empty for no passphrase):
   Enter same passphrase again:
   Your identification has been saved in /root/.ssh/id_rsa.
   Your public key has been saved in /root/.ssh/id_rsa.pub.
   The key fingerprint is:
   SHA256:p3jpR0Zl4TOUqADYXm54MX0NV0r77oMEg3KJJRnlToI root@bastion.example.com
   The key's randomart image is:
   +---[RSA 3072]----+
   |   o...=. .+=+.  |
   |  . ..B.o o==o   |
   |   .E+.Bo= o*    |
   |    o *+= +  +   |
   |     o oS..o  .  |
   |       . +o ..   |
   |      . +o . ..  |
   |       o  . ...  |
   |        ..    .. |
   +----[SHA256]-----+
   [root@bastion ~]#
   [root@bastion ~]# ssh-copy-id bastion
   ...
   [root@bastion ~]# ssh-copy-id tower1
   ...
   [root@bastion ~]# ssh-copy-id tower2
   ...
   [root@bastion ~]# ssh-copy-id tower3
   ...
   [root@bastion ~]# ssh-copy-id db1
   ...
   [root@bastion ~]# ssh-copy-id db2
   ...
   [root@bastion ~]#
   ```

