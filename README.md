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
- Download the docker files from github::: `wget `
```
