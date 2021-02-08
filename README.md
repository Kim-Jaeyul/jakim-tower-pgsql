# Red Hat Automation Platform

## 3 Tower & 2 pgsql 구성도

![image-20210209084632817](C:\Users\jakim\Desktop\navercloud\pic\image-20210209084632817.png)



## 작업목차

1. [사전준비.md](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/01%EC%82%AC%EC%A0%84%EC%A4%80%EB%B9%84.md)
2. [Bastion에 Offline 설치를 위한 Repository 및 관련 설치파일 준비.md](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/02Bastion%EC%97%90%20Offline%20%EC%84%A4%EC%B9%98%EB%A5%BC%20%EC%9C%84%ED%95%9C%20Repository%20%EB%B0%8F%20%EA%B4%80%EB%A0%A8%20%EC%84%A4%EC%B9%98%ED%8C%8C%EC%9D%BC%20%EC%A4%80%EB%B9%84.md)
3. [Bastion에 ansible 설치.md](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/03Bastion%EC%97%90%20ansible%20%EC%84%A4%EC%B9%98.md)
4. [Bastion에 dnsmasq 설정(optional).md](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/04Bastion%EC%97%90%20dnsmasq%20%EC%84%A4%EC%A0%95(optional).md)
5. [Bastion의 public key 배포.md](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/05Bastion%EC%9D%98%20public%20key%20%EB%B0%B0%ED%8F%AC.md)
6. [모든 노드 repository 설정.md](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/06%EB%AA%A8%EB%93%A0%20%EB%85%B8%EB%93%9C%20repository%20%EC%84%A4%EC%A0%95.md)
7. [postgresql 설치하기(host db1).md](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/07postgresql%20%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0(host%20%20db1).md)
8. [postgresql 설치하기(host db2).md](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/08postgresql%20%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0(host%20%20db2).md)
9. [Ansible Tower 설치(host Bastion).md](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/09Ansible%20Tower%20%EC%84%A4%EC%B9%98(host%20%20Bastion).md)
10. [DB2로 failover 시나리오.md](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/10DB2%EB%A1%9C%20failover%20%EC%8B%9C%EB%82%98%EB%A6%AC%EC%98%A4.md)
11. [DB1으로 failback 시나리오.md](https://github.com/Kim-Jaeyul/jakim-tower-pgsql/blob/main/doc/11DB1%EC%9C%BC%EB%A1%9C%20failback%20%EC%8B%9C%EB%82%98%EB%A6%AC%EC%98%A4.md)