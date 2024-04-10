### Hosting a WordPress Website on AWS



#### Project Overview

This project involves setting up a WordPress website on AWS with a highly available and scalable architecture. Key components and steps involved are as follows:

1. **Infrastructure Setup**
   - Configured a Virtual Private Cloud (VPC) with public and private subnets across two availability zones (AZs) for redundancy and fault tolerance.
   - Deployed an Internet Gateway to enable communication between VPC instances and the internet.
   - Utilized Security Groups to control inbound and outbound traffic to EC2 instances.
   - Positioned web servers (EC2 instances) within private subnets for enhanced security.

2. **High Availability and Scalability**
   - Leveraged two availability zones to enhance system reliability.
   - Implemented an Application Load Balancer (ALB) to evenly distribute web traffic across multiple EC2 instances.
   - Used Auto Scaling Groups to automatically manage EC2 instances, ensuring availability, scalability, fault tolerance, and elasticity.

3. **Storage and Database**
   - Stored web files on GitHub for version control and collaboration.
   - Utilized Amazon Elastic File System (EFS) for a shared file system to store WordPress files.
   - Deployed Amazon RDS (Relational Database Service) for the MySQL database to store WordPress data.

4. **Domain Registration and DNS**
   - Registered a domain name and set up DNS records using Amazon Route 53 for domain management.

5. **Security and Monitoring**
   - Secured application communications using AWS Certificate Manager (ACM) for SSL/TLS certificates.
   - Configured Simple Notification Service (SNS) to receive alerts about activities within the Auto Scaling Group.
   - Implemented EC2 Instance Connect Endpoint for secure connections to assets within both public and private subnets.

#### Deployment Script

The following script can be used to automate the deployment of WordPress on an EC2 instance:

```bash
#!/bin/bash

# Update software packages
sudo yum update -y

# Install Apache web server
sudo yum install -y httpd
sudo systemctl enable httpd
sudo systemctl start httpd

# Install PHP and necessary extensions
sudo dnf install -y \
php \
php-cli \
php-cgi \
php-curl \
php-mbstring \
php-gd \
php-mysqlnd \
php-gettext \
php-json \
php-xml \
php-fpm \
php-intl \
php-zip \
php-bcmath \
php-ctype \
php-fileinfo \
php-openssl \
php-pdo \
php-tokenizer

# Install MySQL Server
sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
sudo rpm --import https://repo.mysql.com/RPM-GPG-KEY-mysql-2023
sudo dnf repolist enabled | grep "mysql.*-community.*"
sudo dnf install -y mysql-community-server
sudo systemctl start mysqld
sudo systemctl enable mysqld

# Mount EFS to HTML directory
EFS_DNS_NAME=fs-074850f4af0bb103d.efs.ca-central-1.amazonaws.com
echo "$EFS_DNS_NAME:/ /var/www/html nfs4 nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2 0 0" >> /etc/fstab
mount -a

# Set permissions
sudo chown apache:apache -R /var/www/html

# Restart Apache web server
sudo systemctl restart httpd
```

#### Instructions

1. **Prerequisites**:
   - Ensure you have an AWS account and necessary IAM permissions.
   - Prepare your VPC with public and private subnets, Internet Gateway, Security Groups, and necessary Route 53 configurations.

2. **Deploy EC2 Instance**:
   - Launch an EC2 instance within your VPC, preferably in a private subnet.
   - Attach necessary IAM roles and policies to the instance for accessing other AWS resources.

3. **Run Deployment Script**:
   - Connect to the EC2 instance via SSH.
   - Copy and run the deployment script provided above on the EC2 instance.
   - This script will update the system, install Apache, PHP, MySQL, mount EFS, set permissions, and restart the web server.

4. **Configure WordPress**:
   - Once the script execution is complete, access your EC2 instance's public IP or DNS in a web browser.
   - Follow the WordPress installation wizard to set up and configure your WordPress site.

5. **Monitor and Scale**:
   - Monitor the health of your EC2 instances, ALB, and other AWS resources using CloudWatch.
   - Set up alarms and notifications using SNS for proactive monitoring.
   - Utilize Auto Scaling Groups to automatically scale EC2 instances based on traffic demand.

