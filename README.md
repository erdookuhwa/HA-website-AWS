# Highly Available Website in AWS

This is a simple WordPress website deployed on Elastic Beanstalk which automatically scale web apps. The Storage solution is a shared storage using Amazon Elastic File System (EFS).
The database solution (Amazon RDS) will be external to the Elastic Beanstalk, making it easy for multiple environments to connect to the DB. Another benefit is that you can easily perform blue/green deployment without affecting the database.

#### Step 1 - Launching RDS DB with MySQL Engine
**_Since this is not in Prod, I'll be using the `Dev/Test` template but do note that if you're in Prod then you should use the Production template._**
- I'm selecting the Multi-AZ DB instance... However, note that _there is now a new `Deployment Option`: Multi-AZ DB Cluster which let's you create a primary and 2 readable standby instances, each in a different AZ! With this new option, your standby supports and can serve read workloads! :clap:_
- Do take note of your Master username & password. _You'll need them to connect to the DB later._
- In the Connectivity options, take note of your `VPC`, you'd need to place your app in the same `VPC` later on so they're within the same network. Also, if you plan to connect to your DB instance outside of this `VPC` network, then you need to select the option to make it _Publicly Accessible_. When configuring your _Security Group_, if you set it Publicly accessible, then configure inbound rules to allow the IP or block of IPs you want to have access and of course, allow access fromt the application (preferrably via the app SG).

#### Step 2 - Downloading WordPress (WP)
_Do this part in `CloudShell` so your files are readily accessible from within AWS_
- Download the WP files using: `curl https://wordpress.org/wordpress-6.1.1.tar.gz -o wordpress.tar.gz`
- Download the config files from the sample repo: `wget https://github.com/aws-samples/eb-php-wordpress/releases/download/v1.1/eb-php-wordpress-v1.zip`
- Extract the WordPress files. `tar -xvf wordpress.tar.gz` _When it's done extracting, it creates a new directory `wordpress` and moves the extracted contents here_.
- You can skip this and `cd wordpress`. _I chose to rename mine `mv wordpress wp-bnstlk`_
- `cd wp-bnstlk`

#### Step 3 - Launching an Elastic Beanstalk Environment
- You can use [this pre-configured URL](https://us-east-1.console.aws.amazon.com/elasticbeanstalk/home#/newApplication?applicationName=eve-elb-app&environmentType=LoadBalanced) to create your app.
- For _Platform_, select `PHP` and accept defaults > _Review and Launch_ > _Create app_. It takes a few minutes as it is creating other resources like your EC2 instance, instance SG, LB, LB SG, ASG, CloudFormation Stack, S3 Bucket, CloudWatch alarms, domain name (_if you prefer, you can choose your domain name just before Review and Launch, I chose to leave mine blank so I'll get an auto-generated domain._)


#### Step 4 - Configure Security Groups & Environment Variables
- In your environment, under Configuration, Edit the _Instances_ option. Edit EC2 SG to make sure that it's letting the DB traffic on Port 3306 from the rds-SG Security Group. 
- Still in _Configuration_, navigate to **Software** this time > _Edit_ > Scroll down to _Environment Properties_ and enter the following:

- | Name             | Property                                                   |
  |-----             | --------                                                   |
  |`RDS_HOSTNAME`    | This is the Endpoint of your DB instance                   |
  |`RDS_DB_NAME`     | The name of the database you create                        |
  |`RDS_PASSWORD`    | The password you created when setting up your RDS instance |
  |`RDS_USERNAME`    | The username you created when setting up your RDS instance | 
  |`RDS_PORT`        | MySQL instance will run on Port 3306                       | 

- When you navigate to your app URL, you should get a page that looks like this:
-
![Screenshot 2023-01-31 at 06 41 06](https://user-images.githubusercontent.com/64602124/215809050-2afaaefc-5b09-4cd2-8e52-36756e938c69.png)


#### Step 5 - Update config files & Create Source Bundle
- Create source bundle using ` zip ../wp-bnstlk.zip -r * .[^.]*`
- Download the created source bundle `wp-bnstlk.zip`. Go to _Actions_ > _Download file_ and provide the file path from CloudShell
- In the Elastic Beanstalk env in the Management Console, click on _Upload and deploy_ > upload the zip file you just downloaded.
- Open website URL and perform the standard installation. Use the same settings previously configured for the _Environment properties_.


#### Step 6 - Installing and Setting Up WordPress
- Navigate to your app URL in Elastic Beanstalk. If all works correctly, you should be prompted with the installation for WP.
-



#### Step 6 - Set up AutoScaling
For ensure High Availability, configure AutoScaling to have a minimum of 2 instances running at any given time. 
- Back in the Environment, _Configuration_, edit **Capacity**, at the very top, update _Auto scaling group_ **_Min_** from 1 to **2** > _Apply_.
- 







... to be continued ... :v:
