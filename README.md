# Laravel_AWS_ElasticBeanstalk
How to host a Laravel application on AWS Elastic Beanstalk

## 1) Create an AWS account if you don't already have one
## 2) Create an RDS instance
#### I used a MySQL instance with the following settings;
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

