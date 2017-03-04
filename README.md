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
##### I used the following settings;
- Access type = Programmatic access
- Create new group and add the following permissions; <i>AmazonRDSFullAccess, AWSElasticBeanstalkFullAccess</i>
- Make sure the new user is added to the group
- Create an access key for the user and keep the <i>'key id'</i> and <i>'secret access key'</i> in a safe place or download the csv and store securely

#### 4) Create an EC2 keypair
- From the EC2 dashboard --> network & security --> Key pairs --> Create key pairs
- Provide a name and download the .pem file (store securely for use later on)

#### 5) Install Elastic Beanstalk CLI on your local PC
- On MacOS this is simple with Homebrew; <i>brew install awsebcli</i>

#### 6) Initialise the EBS instance
- From the CLI on your local PC, navigate to the root of your project
- Start the initialisation wizard; <i>eb init</i>

##### I used the following settings;
- Default region (3); us-west-2
- Name your App and Environment
- Select 'Y' to PHP and choose PHP version 7.0
- Setup SSH and select the keypair created earlier

#### 7) Create an EBS environment options file
- Create a new file in the .elasticbeanstalk directory of your project with the following name;<br> 
<i>'optionsettings.environment-name'</i>
- Add the following code to the file;
```
[aws:autoscaling:asg]
Custom Availability Zones=us-west-2a (use the same AZ as used for the MySQL RDS instance deployed earlier)
MaxSize=1
MinSize=1

[aws:autoscaling:launchconfiguration]
EC2KeyName=keypair-name (enter the keypair name as created earlier)
InstanceType=t2.micro

[aws:autoscaling:updatepolicy:rollingupdate]
RollingUpdateEnabled=false

[aws:ec2:vpc]
Subnets=
VPCId=

[aws:elasticbeanstalk:application]
Application Healthcheck URL=

[aws:elasticbeanstalk:application:environment]
PARAM1=
PARAM2=
PARAM3=
PARAM4=
PARAM5=

[aws:elasticbeanstalk:container:php:phpini]
allow_url_fopen=On
composer_options=--no-dev
display_errors=Off
document_root=/public
max_execution_time=60
memory_limit=256M
zlib.output_compression=Off

[aws:elasticbeanstalk:hostmanager]
LogPublicationControl=false

[aws:elasticbeanstalk:monitoring]
Automatically Terminate Unhealthy Instances=true

[aws:elasticbeanstalk:sns:topics]
Notification Endpoint=
Notification Protocol=email
```
#### 8) Create an Environment Variables file
- Create a new file in the .elasticbeanstalk directory of your project with the following name;<br> <i>'00environmentVariables.config'</i>
- Add the following code to the file;
```
option_settings:
   - namespace: aws:elasticbeanstalk:application:environment
     option_name: DB_HOST
     value: mysqldbname.dragegavysop.us-east-1.rds.amazonaws.com
   - option_name: DB_PORT
     value: 3306
   - option_name: DB_NAME
     value: dbname
   - option_name: DB_USER
     value: username
   - option_name: DB_PASS
     value: password
```
#### 9) Create a Composer Config file
- Create a new file in the .elasticbeanstalk directory of your project with the following name;<br>
<i>'01composer.config'</i>
- Add the following code to the file;
```
commands:
   01updateComposer:
      command: export COMPOSER_HOME=/root && /usr/bin/composer.phar self-update

option_settings:
   - namespace: aws:elasticbeanstalk:application:environment
     option_name: COMPOSER_HOME
     value: /root

container_commands:
   01optimize:
      command: "/usr/bin/composer.phar dump-autoload --optimize"
```
#### 10) Create a Artisan Config file
- Create a new file in the .elasticbeanstalk directory of your project with the following name;<br>
<i>'02artisan.config'</i>
- Add the following code to the file;
```
container_commands:
   01migrateSeed:
      command: "php artisan migrate --force"
   02seed:
      command: "php artisan db:seed --force"
```
#### 11) Edit your application database config file
- Edit the <i>'database.php'</i> file in the <i>config</i> directory of your project with the following changes;
```
        'mysql' => [
            'driver' => 'mysql',
            'host' => $_ENV['DB_HOST'],
            'port' => $_ENV['DB_PORT'],
            'database' => $_ENV['DB_NAME'],
            'username' => $_ENV['DB_USER'],
            'password' => $_ENV['DB_PASS'],
            'charset' => 'utf8',
            'collation' => 'utf8_unicode_ci',
            'prefix' => '',
        ],
```

