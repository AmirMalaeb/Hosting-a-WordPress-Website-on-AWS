WordPress Hosting on AWS

This project demonstrates the deployment of a WordPress website on AWS, using a Virtual Private Cloud (VPC) created from scratch with various AWS services to ensure scalability, fault tolerance, and security. The website is hosted on EC2 instances within private subnets, and traffic is distributed using an Application Load Balancer. The infrastructure also uses Amazon RDS for the database, EFS for shared storage, and EC2 Instance Connect Endpoint Service for secure SSH access to instances in private subnets.

Architecture Overview

The architecture includes:

	- A custom Virtual Private Cloud (VPC) with both public and private subnets across two availability zones.
	- EC2 instances in private subnets hosting the WordPress website.
	- Application Load Balancer (ALB) distributing traffic across EC2 instances.
	- NAT Gateway enabling internet access for instances in private subnets.
	- Auto Scaling Group (ASG) ensuring scalability and fault tolerance.
	- Route 53 for domain name registration and DNS configuration.
	- EFS for shared storage across EC2 instances.
	- Amazon RDS for database management.
	- Security Groups to control traffic to the EC2 instances.
	- SSL/TLS encryption using AWS Certificate Manager.
	- Simple Notification Service (SNS) for notifications about scaling events.
	- EC2 Instance Connect Endpoint Service for secure SSH access to EC2 instances within the private subnets.

AWS Resources Used

	1.	Virtual Private Cloud (VPC): Created from scratch with public and private subnets across two availability zones.
	2.	Internet Gateway: Facilitates communication between VPC instances and the Internet.
	3.	Security Groups: Network firewall mechanism to control traffic to web servers.
	4.	Application Load Balancer (ALB): Distributes traffic across EC2 instances in multiple availability zones.
	5.	EC2 Instances: Hosts the WordPress website within private subnets for enhanced security.
	6.	Auto Scaling Group: Automatically manages EC2 instances based on traffic.
	7.	NAT Gateway: Allows instances in private subnets to access the Internet.
	8.	Elastic File System (EFS): Provides shared storage for WordPress files across EC2 instances.
	9.	Amazon RDS: Managed MySQL database for WordPress.
	10.	Route 53: Domain name registration and DNS configuration.
	11.	AWS Certificate Manager: Provides SSL/TLS certificates for secure website communication.
	12.	Simple Notification Service (SNS): Sends notifications related to Auto Scaling activities.
	13.	EC2 Instance Connect Endpoint Service: Used to securely SSH into instances in private subnets without requiring bastion hosts or public IPs.

WordPress Installation Steps

The following steps were executed on the EC2 instances to install and configure WordPress:

	1.	Update software packages:
    
    ```bash
    sudo yum update -y


    2.	Create the web root directory:
 
    sudo mkdir -p /var/www/html
    ```

    3.	Mount EFS to the web root directory:
    
    sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport "$EFS_DNS_NAME":/ /var/www/html
    ```

    4.	Install Apache and PHP:

    sudo yum install -y httpd
    sudo systemctl enable httpd
    sudo systemctl start httpd
    ```

    5.	Install PHP 8 and necessary extensions:

    sudo dnf install -y php php-cli php-cgi php-curl php-mbstring php-gd php-mysqlnd php-gettext php-json php-xml php-fpm php-intl php-zip php-bcmath php-ctype php-fileinfo php-openssl php-pdo php-tokenizer
    ```

    6.	Install MySQL:

    sudo wget https://dev.mysql.com/get/mysql80-community-release-el9-1.noarch.rpm
    sudo dnf install -y mysql80-community-release-el9-1.noarch.rpm
    sudo dnf install -y mysql-community-server
    sudo systemctl start mysqld
    sudo systemctl enable mysqld
    ```

    7.	Set permissions:

    sudo usermod -a -G apache ec2-user
    sudo chown -R ec2-user:apache /var/www
    sudo chmod 2775 /var/www && find /var/www -type d -exec sudo chmod 2775 {} \;
    sudo find /var/www -type f -exec sudo chmod 0664 {} \;
    chown apache:apache -R /var/www/html
    ```

    8.	Download and configure WordPress:

    wget https://wordpress.org/latest.tar.gz
    tar -xzf latest.tar.gz
    sudo cp -r wordpress/* /var/www/html/
    sudo cp /var/www/html/wp-config-sample.php /var/www/html/wp-config.php
    sudo vi /var/www/html/wp-config.php #use to edit the wep-config file with your database name, password, etc..
    ```

    9.	Restart Apache:

    sudo service httpd restart

    ```

    ### Auto Scaling Group Launch Template Script

    This script is included in the launch template for the Auto Scaling Group, ensuring that new instances are configured correctly with the necessary software and settings. You can find it in the files.

    Secure SSH Access

    For secure SSH access to instances in the private subnets, EC2 Instance Connect Endpoint Service was utilized. This allows SSH connections to the EC2 instances without the need for bastion hosts or public IP addresses, ensuring secure and controlled access.

    Monitoring and Alerts

	•	SNS: Configured to send notifications related to activities within the Auto Scaling Group, such as scaling events.

    ## How to Use

    1. Clone this repository to your local machine.
    2. Follow the AWS documentation to create the required resources (VPC, subnets, Internet Gateway, etc.) as outlined in the architecture overview.
    3. Use the provided scripts to set up the WordPress application on EC2 instances within the VPC.
    4. Configure the Auto Scaling Group, Load Balancer, and other services as per the architecture.
    5. Access the WordPress website through the Load Balancer's DNS name.

    Conclusion

    This project leverages multiple AWS services to ensure the scalability, fault tolerance, and security of a WordPress website. The deployment is designed to handle varying levels of traffic while maintaining high availability and performance. The use of EC2 Instance Connect Endpoint Service ensures secure SSH access to private instances, eliminating the need for public IPs or bastion hosts.

    ## Contributing

    Contributions to this project are welcome! Please fork the repository and submit a pull request with your enhancements.

    ## License

    This project is licensed under the MIT License - see the LICENSE file for details. 