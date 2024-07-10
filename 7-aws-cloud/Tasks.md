## Task 2 : EC2 Deep Dive

- Create 2 Ec2 instance with server1 and server2
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
  Attach the 2 instances in the load balancer and check till the health-status is in service
  Copy the Load balancer DNS name Info in the browser. Now you will see that the browser output will switch from ec1 instance

**************************************************************************************************************************************

Task 3: S3 Bucket

Task 4: IAM

Task 5: Lambda
- create an ec2 instance on the AZ
- Using  the lamda function we are going to stop and start the Ec2

  ## start ec2
  
```
import boto3
import os

region = os.environ['region']
tagname = os.environ['tagname']
tagvalue = os.environ['tagvalue']



def lambda_handler(event, context):
    ec2 = boto3.client('ec2', region_name=region)
    response = ec2.describe_instances(
        Filters=[
            {
                'Name': 'tag:'+tagname,
                'Values': [tagvalue]
            }
        ]
    )

    instancelist = []
    for reservation in (response["Reservations"]):
        for instance in reservation["Instances"]:
            if instance["State"]['Name'] == "stopped":
                instancelist.append(instance["InstanceId"])

    if instancelist != []:
        ec2.start_instances(InstanceIds=instancelist)
        for instance in instancelist:
            print('instance name: ' + str(instance))
```

- Please input the below fileds under the environmental variables

```
region: us-east-1
tagname: env
tagvalue: prod
```
