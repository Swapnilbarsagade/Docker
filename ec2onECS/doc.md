###############Hosting container on ECS with ec2 for nginx##########################################

Step 1: Create cluster on ECS.

    -> Give Cluster name and select EC2 instance
    -> Select c1.xlarge instance type.
    -> Set minimun desire capacity = 1 and maximum desire capacity = 2.
    -> Set Root EBS volume size = 40.
    -> Auto-assign public IP = Turn on  and create cluster.

Step 2: Create a new task definition.

    -> Give appropriate task definition family.
    -> Select infrastructure launch type as ec2.
    -> Network mode = bridge
    -> CPU = 2 vCPU and Memory = 4 GB
    -> container name = give apropriate name
    -> Image URI = nginx:latest
    -> host port = 80 and container port = 80

Step 3: Create service in created cluster.

    -> Select launch type as ec2.
    -> Select application type as task and select task definition family.
    -> Create service.

Step 4: Go to ec2 dashboard and copy instance ip .

    -> Open http port 80  for nginx
    -> copy ec2 ip and you'll get the default home page of nginx.
