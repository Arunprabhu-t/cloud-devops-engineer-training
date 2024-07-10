## Task 2 : EC2 Deep Dive

- Create 2 Ec2 isnatnces with server1 and server2
- Select Key-pair aws-unix
- Select the security group rangarajbk_allowall
-  Add the below in user-data

```
#!/bin/bash
yum install httpd -y
service httpd start
chkconfig httpd on
hostname > /var/www/html/index.html
```

- Create an efs filesystem on the  same az your created the ec2 instance 

```
sudo su - 
mkdir rangarajbk
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport 172.31.29.208:/ /root/rangarajbk
```

- Create an LB and attach the 2 instances 
