
![[Pasted image 20240328173650.png]]

## Resource Details

|   |   |   |
|---|---|---|
  |Resources|Name|Specification|
  |ECS Cluster|exabler-live-py--ecs-prod|Capacity Provider EC2 instances|
  |ECS Service|exabler-live-py-new-serive (Active)|Created with ECS subnet|
  |ECR Repository|fast-api|Contains application image|
  |Task Definition|exabler-live-py-prod-ecs|Uses existing ECS volume|
  |RDS|exabler-live-db-prod|MySQL Aurora Serverless v2|
  |Target Group|exabler-lb-prod-tg|Type - IP|
  |Load Balancer|exabler-lb-prod|Type - Application|

### ECR Repository

1. Create repository

|   |   |
|---|---|
 |Option|Selection|
|Visibility settings|Private|
|Name|fast-api|
|Scan on push|Enabled|

### ECS Cluster

Below are the steps to create an ECS Cluster

1. Login to AWS --> Select Region London (eu-west-1)
2. Go to ECS --> Create Cluster
3. Provide Cluster Name like exabler-live-py-ecs-prod
4. In Infrastructure

|   |   |
|---|---|
 |Option|Selection|
 |Amazon EC2 instances|On-demand|
 |OS|Amazon Linux 2|
 |Instance type|t2.micro|
 |Role|ecsInstanceRole|
 |Desired  Capacity|Min 1 Max 2|
 |Select key pair|Create new or use existing|
 |EBS volume size|30 GB|
 
   
5. In Network settings,

|   |   |
|---|---|
 |Option|Selection|
 |VPC|default|
 |Subnets|ECS Subnet|
 |Security Group|Create or use existing|
 |Auto Assign Public IP|Use Subnet settings|


### ECS Task Definition

Below are the steps to create ECS Task Definition

1. Go to AWS ECS --> Select Task Definitions --> Create new task definition
2. Provide Name like exabler-live-py-prod-ecs
3. In Infrastructure requirements 

|   |   |
|---|---|
 |Option|Selection|
|Launch Type|EC2 instances|
|OS|EC2 instances|
|Network Mode|awsvpc|
|Task Size|0.8 CPU & 0.8 Mem|
|Task Role|ecsTaskExecutionRole|

4. Container 1

|   |   |
|---|---|
 |Option|Selection|
|Name|fast-api|
|Image URI|From ECR|
|Essential container|Yes|
|Container Port|80|
|Log Collection|Amazon Cloud Watch|

 5. Storage --> Add volume

|   |   |
|---|---|
 |Option|Selection|
|Name|exabler-live-py-prod-ecs-volume|
|Volume type|EFS|
|FS ID|Select existing|

6. Create Task Definition

### Target Group

1. Go to EC2 Console --> Target Group --> Create Target Group

|   |   |
|---|---|
 |Option|Selection|
|Target Type|IP addresses|
|Name|exabler-lb-prod-tg|
|Protocol:Port|HTTP:80|
|IP address type|IPv4|
|VPC|Default|
|Protocol Version|HTTP1|
|Health Checks|HTTP|
|Path|/|

### Application Load Balancer

1. Go to EC2 Console --> Load Balancer --> Create Load Balancer

|   |   |
|---|---|
 |Option|Selection|
|Type|Application|
|Name|exabler-lb-prod|
|Scheme|Internet Facing|
|IP address type|IPV4|
|Network mapping|Default VPC All AZs|
|Security groups|Select Existing|
|Listeners and routing||
|Protocol:Port|HTTP:80|
|Action|Forward to created target group|

### ECS Service

1. Go to AWS ECS --> Select existing cluster
2. Create Service

|   |   |
|---|---|
 |Option|Selection|
|Capacity provider strategy|Use cluster default|
|Application type|Service|
|Family|Select cluster name|
|Service name|exabler-live-py-ecs-prod|
|Desired Task|1|
|VPC|Default|
|Subnet|subnet-03a4311a1e6b11a67 (private-subnet-2a-for-ecs)|
|Security group|Create or use existing|
|Load balancer type|Application|
|Container|fast-api 80:80|
|Application Load Balancer|Use existing|
|Use an existing listener|80:HTTP|

### RDS

1. Go to AWS RDS --> Create Database

|   |   |
|---|---|
 |Option|Selection|
|Engine options|Aurora|
|Engine Version|3.04.1|
|Templates|Production|
|DB cluster identifier|exabler_live_db_prod|
|Master username|exabler_live_manager_prod|
|Credentials management|User defined|
|Configuration options|Aurora Standard|
|Instance configuration|Serverless v2|
|Capacity range|0.5 -16 ACU|
|Multi-AZ deployment|Create an Aurora Replica or Reader node|
|VPC|Default|
|jump-box-dev-subnet-group||
|Public access|No|
|Existing VPC security groups|Select SG|
|AWS KMS key|aws/rds|

