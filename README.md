# Hosting a two tier website on AWS-ECS-Serverless-Fargate using docker
## Tools we are using
### VPC
### Dynamo DB
### ECS - fargate
### ECR
### Docker
### Application Load Balancer
### IAM
### EC2
### RDS
### Secrets Manager
## Create a VPC
```sh
## Use the AWS console
```
## VPC
```sh
## Create a VPC with the following specs
   - 2 AZ's (High Durability)
   - 2 Public Subnets (Both internet facing, one is active one is for back up)
   - 4 private subnets (First two are for the container,docker and the database and the other two are backup )
   - A common NAT gateway (We need this for our private instances to access the net)
```
## Security Group
```sh
## Create Secuity Groups
   - Intenet access:: The first one is to give access from anywhere in the internet http: (0.0.0.0/0)
   - App:: The second one is to only allow the ALB to give access to the Containers
   - RDS Database:: The third one is on port 3306. It gives the app access to the RDS database
```
## ECR
```sh
## Create a repo in ECR
   - Create a private repo and note the URI
```
## Docker
```sh
Build a docker image
```
## IAM
```sh
On IAM we need to add permissions for:
- EC2 to build a container using ECR -  We are able to upload ECR images >EC2InstanceProfileForImageBuilderECRContainerBuilds 
- SSM managed instance core - Remotely connect to an instance using sessions manager >AmazonSSMManagedInstanceCore
```
## EC2
```sh
Using the IAM we now create an EC2 instance that we will use to uplaod our docker file and upload content to ECR
- We now connect to the EC2 using sessions manager
- Change User::: `sudo su - ec2-user`
- Check for updates::: `sudo yum update -y`
- Install docker::: `sudo yum install docker`
- Start docker::: `sudo service docker start`
- Place EC2 user in the docker group::: `sudo usermod -a -G docker ec2-user`
- Restart the instance::: `exit`
- Download the docker files from github::: `wget https://github.com/iaasacademy/aws-how-to-guide/raw/238deeefb955ddef46c673f5154754f679410d57/amazon-ecs-mini-project/ritual-roast-code.zip`
- Unzip the file::: `unzip ritual-roast.zip`
- Buid docker::: `docker build -t 123345876543.dkr.ecr.us-east-1.amazonaws.com/ritual-roast .`
- Confirm the images in docker::: `docker images`
```
## ECR
```sh
Push docker images to ECR
- Retrieve an authentication token and authenticate your Docker client to your registry.
- Run the command to push image to your newly created AWS repository.
```
## RDS
```sh
Create an RDS MYSQL DB
We have a VPC (created above)
We need to have a subnet group which connects the subnets of AZ1 and AZ2 (standard for all RDS)
We will have both a `master and standby instance`
- Create a subnet group and associate the two subnets created in virtual-roast VPC to it
- Create the MYSQL database
```
## Secrets Manager
```sh
 - Create a security group on our VPC for the database to talk to itself since it doesn't allow traffic from somewhere else this will make rotation of passwords by secrets manager very easy
- Create the secrets manager and then create a lambda that rotates the password after 7 days
```
## Application Load Balancer
```sh
A prerequisite to using an ALB is to make a target group with the fargate instances
We are going to be using IP addresses and not Instances when we specify target group details
- We use a file called `health.html`
- Create an ALB and choose our target groups
I noted that we can't have a target group/ ALB using the default VPC, we need to use a custom VPC that has internet facing subnets (HTTP)
```
## IAM Execution roles
```sh
We are gonna set up a single role with a couple of policies
- ECS needs to download the image and send log info to cloudwatch
 - ECS role (we select task )
   - AmazonECSTaskExecutionRolePolicy (ECR access and send logs to cloudwatch)
   - Allow ECS to read secret manager
- We want the ECS to access the secrets manager for the passwords
```
## ECS Cluster
```sh
Create an ECS cluster
```
## Create an ECS Task definition
```sh
We bring together env vars, secret manager secrets, and roles together to define the task
```


