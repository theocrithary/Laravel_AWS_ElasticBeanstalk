# Laravel_AWS_ElasticBeanstalk
How to host a Laravel application on AWS Elastic Beanstalk

#### 1) Create an AWS account if you don't already have one
#### 2) Create an RDS instance
##### I used a MySQL instance with the following settings;
- Dev/Test (to be eligible for free tier)
- DB Instance Class = db.t2.micro
- Multi-AZ = no
- Storage Type = Magnetic
- Allocated Storage = 5GB
- Provide the required unique identifier, username, password
- Select defaults where already populated
- Choose default VPC
- Provide a DB Name
- Change backup retention to 0 if you do not need backups

#### 3) Create a new IAM User
##### I used the below settings;
- Acces type = Programmatic access
- Create new group and add the following permissions; <i>AmazonRDSFullAccess, AWSElasticBeanstalkFullAccess</i>
- Make sure the new user is added to the group
- Create an access key for the user and keep the <i>'key id'</i> and <i>'secret access key'</i> in a safe place or download the csv and store securely

#### 4) Create an EC2 keypair
- From the EC2 dashboard --> network & security --> Key pairs --> Create key pairs
- Provide a name and download the .pem file (store securely for use later on)

#### 5) Install Elastic Beanstalk CLI on your local PC
- On MacOS this is simple with Homebrew; <i>brew install awsebcli</i>

#### 6) Initialise the EB instance
- From the CLI on your local PC, navigate to the root of your project
- Start the initialisation wizard; <i>eb init</i>

##### I used the below settings
- Default region (3); us-west-2
- Name your App and Environment
- Select 'Y' to PHP and choose PHP version 7.0
- Setup SSH and select the keypair created earlier

