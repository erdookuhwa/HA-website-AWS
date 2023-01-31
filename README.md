# Highly Available Website in AWS

This is a simple WordPress website deployed on Elastic Beanstalk which automatically scale web apps. The Storage solution is a shared storage using Amazon Elastic File System (EFS).
The database solution (Amazon RDS) will be external to the Elastic Beanstalk, making it easy for multiple environments to connect to the DB. Another benefit is that you can easily perform blue/green deployment without affecting the database.

#### Step 1 - Launching RDS DB with MySQL Engine
**_Since this is not in Prod, I'll be using the `Dev/Test` template but do note that if you're in Prod then you should use the Production template._**
- I'm selecting the Multi-AZ DB instance... However, note that _there is now a new `Deployment Option`: Multi-AZ DB Cluster which let's you create a primary and 2 readable standby instances, each in a different AZ! With this new option, your standby supports and can serve read workloads! :clap:_
- Do take note of your Master username & password. _You'll need them to connect to the DB later._
- In the Connectivity options, take note of your `VPC`, you'd need to place your app in the same `VPC` later on so they're within the same network. Also, if you plan to connect to your DB instance outside of this `VPC` network, then you need to select the option to make it _Publicly Accessible_. When configuring your _Security Group_, if you set it Publicly accessible, then configure inbound rules to allow the IP or block of IPs you want to have access and of course, allow access fromt the application (preferrably via the app SG).

#### Step 2 - Downloading WordPress


#### Step 3 - Launching an Elastic Beanstalk Environment
- You can use [this pre-configured URL](https://us-east-1.console.aws.amazon.com/elasticbeanstalk/home#/newApplication?applicationName=eve-elb-app&environmentType=LoadBalanced) to create your app.
- For _Platform_, select `PHP` and accept defaults > _Review and Launch_ > _Create app_. It takes a few minutes as it is creating other resources like your EC2 instance, instance SG, LB, LB SG, ASG, CloudFormation Stack, S3 Bucket, CloudWatch alarms, domain name (_if you prefer, you can choose your domain name just before Review and Launch, I chose to leave mine blank so I'll get an auto-generated domain._)

... to be continued ... :v:
