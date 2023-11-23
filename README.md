## Hosting WordPress Application.

####  Hosting a WordPress application across multiple availability zones entails deploying an RDS instance with a standby, and connecting them via EFS. This setup is complemented by a load balancer and an autoscaling group, ensuring high availability and fault tolerance.

#### Resources used in this project:
- VPC WITH PRIVATE AND PUBLIC SUBNETS
- SECURITY GROUPS 
- EC2
- RDS
- ELASTIC FILE SYSTEM
- NAT GATEWAY
- APPLICATION LOAD BALANCER
- CERTIFICATE MANAGER
- AUTO-SCALING GROUPS
- IAM

### Project Architecture:
https://github.com/baglaaarav/wordpress/blob/main/diagram.pdf

### What I did:

I began by creating a user in AWS Identity and Access Management (IAM) and granting it the necessary permissions. Afterward, I logged in using the IAM user credentials and proceeded to set up a Virtual Private Cloud (VPC) and its associated subnets.

The VPC was configured with a Classless Inter-Domain Routing (CIDR) block of 10.0.0.0/16. Subsequently, an internet gateway was created and attached to the VPC. Two public subnets were established in different availability zones: the first with CIDR 10.0.0.0/24 in us-east-1a and the second with CIDR 10.0.1.0/24 in us-east-1b.

To enable internet access for the public subnets, a route table was created, specifying a route of 0.0.0.0/0 through the internet gateway. Both public subnets were then associated with this route table. Following this, four private subnets were created—two for EC2 instances and two for databases—with CIDR blocks 10.0.2.0/16 (us-east-1a), 10.0.3.0/16 (us-east-1b), 10.0.4.0/16 (us-east-1a), and 10.0.5.0/16 (us-east-1b).

For the private subnets, two additional route tables were established, each associated with the respective availability zone. These route tables were configured to manage the internal traffic within the private subnets.

To enable internet communication for EC2 instances within the private subnets, I proceeded to create NAT gateways. Initially, I allocated two Elastic IP addresses and then deployed two NAT gateways, each associated with one of the public subnets.

To facilitate the secure transfer of data, I proceeded by creating five security groups. The first one was designated for Elastic File System (EFS), the second for the web server/EC2 instances, the third for the database, the fourth for the Application Load Balancer (ALB) security group, and the fifth specifically for SSH, which would be used in configuring WordPress to EFS.

Upon the creation of these security groups, I then crafted a subnet group incorporating private subnets. Subsequently, I established a MySQL Relational Database Service (RDS) database. The primary database was situated in us-east-1a, with a standby replica in us-east-1b. During this phase, I observed the configuration settings and endpoints, which I will be utilizing in the subsequent steps of the process.

Now it's time to build the Elastic File System (EFS), where I configured it for access across multiple availability zones. I achieved this by adding the subnets created for the database and attaching the security group created for the file system. 

Up to this point, all the necessary components are in place to install the WordPress application on our Elastic File System (EFS) and connect it to the database. I initiated the process by creating an EC2 instance in Public Subnet 1 and establishing a connection to it. Subsequent to the connection, I installed PHP, MySQL, and Apache, and proceeded to mount my EFS using the code obtained from the EFS interface.

Following this, I downloaded the WordPress files and moved them to the /var/www/html directory. I edited the wp-config.php file, incorporating the RDS configuration and endpoint information. A crucial step involved restarting the HTTP server (httpd).

With the public IP of the EC2 instance, I navigated to the WordPress configuration page in a new window. As a result, the WordPress setup process was initiated, leveraging the pre-configured database and the EFS file system.  At this stage, the database and EFS file system are seamlessly integrated with the WordPress application.

Now it's time to create a load balancer and an autoscaling group, but before proceeding, I created a launch template with the necessary configurations for EC2 instances and a bash script for connecting to the instances. Subsequently, I established an autoscaling group using the previously configured launch template and created a load balancer within it, incorporating the appropriate security group settings.

This load balancer efficiently distributes traffic evenly between the two EC2 instances while the autoscaling group dynamically adjusts the number of instances based on CPU utilization. Upon completion of these configurations, I updated the domain from the WordPress website to the Load Balancer endpoint.

In summary, the AWS infrastructure has been meticulously designed, encompassing IAM user setup, VPC creation, subnet configuration, and the establishment of secure connectivity. The deployment seamlessly integrates various services, including RDS, EFS, and EC2 instances, optimizing for scalability, high availability, and security. The final touch involves load balancing and autoscaling, ensuring efficient traffic distribution and resource utilization in a robust, highly available, scalable, and secure deployment.


