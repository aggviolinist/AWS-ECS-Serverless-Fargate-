# Hosting a two tier website on AWS-ECS-Serverless-Fargate using docker
## Tools we are using
### VPC
### Dynamo DB
### ECS - fargate
### Docker
### Application Load Balancer
## Create a VPC
1. Use the AWS console
2. Create a VPC with the following specs
   - 2 AZ's (High Durability)
   - 2 Public Subnets (Both internet facing, one is active one is for back up)
   - 4 private subnets (First two are for the container,docker and the database and the other two are backup )
   - A common NAT gateway (We need this for our private instances to access the net)
3. Create Secuity Groups
   - Intenet access:: The first one is to give access from anywhere in the internet http: (0.0.0.0/0)
   - App:: The second one is to only allow the ALB to give access to the Containers
   - RDS Database:: The third one is on port 3306. It gives the app access to the RDS database
