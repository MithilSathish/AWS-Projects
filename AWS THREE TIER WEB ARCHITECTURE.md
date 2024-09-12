**PROJECT: AWS THREE TIER WEB ARCHITECTURE**

**Architecture:**

![image](https://github.com/user-attachments/assets/b24ecea7-a646-43e4-96d4-2e80b9972e4a)

In this architecture:

1.  **Public-Facing Load Balancer**:
    -   An **Application Load Balancer** (ALB) handles client traffic and forwards it to the web tier EC2 instances.
2.  **Web Tier**:
    -   EC2 instances running **Nginx** webservers serve a **React.js** website.
    -   Nginx redirects API calls to the internal facing load balancer.
3.  **Internal Facing Load Balancer**:
    -   This load balancer forwards traffic to the application tier.
4.  **Application Tier**:
    -   Written in **Node.js**, this tier processes data and interacts with the database.
5.  **Database**:
    -   **Aurora MySQL** with multi-AZ deployment for high availability and durability.
6.  **Load Balancing & Autoscaling**:
    -   Load balancing, health checks, and autoscaling groups are implemented at each layer to ensure the architecture's availability and performance.

**S3 Bucket Creation**

1.  Created a new S3 bucket in the AWS Management Console.
2.  This bucket is where we will upload our code later.

![image](https://github.com/user-attachments/assets/8bd80e74-d33a-4bc5-b5c9-0e18212d896c)


**IAM EC2 Instance Role Creation**

1.  Created an EC2 role in the IAM dashboard.
2.  Set EC2 as the trusted entity.
3.  Added the policies **AmazonSSMManagedInstanceCore** and **AmazonS3ReadOnlyAccess.**
4.  Named the role as **3tier** and created it."

![image](https://github.com/user-attachments/assets/0b10563a-602e-43c1-9110-23257e69b14d)



1.  **Networking and Security**
-   Create an isolated network with the following components:
    -   VPC
    -   Subnets
    -   Route Tables
    -   Internet Gateway
    -   NAT gateway
    -   Security Groups

**VPC** (**CIDR range** : 10.0.0.0/16)

![image](https://github.com/user-attachments/assets/021313d7-4383-4b93-a53e-d2cd528c345c)



**Subnet**

**Six** subnets across **two** availability zones. That means that **three** subnets will be in one availability zone, and three subnets will be in another zone.

| **us-east-2b**                       | **us-east-2c**                       |
|--------------------------------------|--------------------------------------|
| Public-Subnet-AZ-1 (10.0.0.0/24)     | Public-Subnet-AZ-2 (10.0.3.0/24)     |
| Private-Subnet-AZ-1 (10.0.1.0/24)    | Private-Subnet-AZ-2 (10.0.4.0/24)    |
| Private-DB-Subnet-AZ-1 (10.0.2.0/24) | Private-DB-Subnet-AZ-2 (10.0.5.0/24) |

![image](https://github.com/user-attachments/assets/227efd19-c512-4ecc-bc29-3ed82cd761dc)



**Internet Gateway**

![image](https://github.com/user-attachments/assets/21b2dd6b-3e66-468b-b1ac-5e3d79d6d715)


-   After creating the internet gateway, attach it to your VPC
-   Then, select the correct VPC and click **Attach internet gateway**.

![image](https://github.com/user-attachments/assets/aca52347-1b95-4b7b-aa29-2ff59168da91)



Internet Gateway Successfully Attached to VPC

![image](https://github.com/user-attachments/assets/1ccb3eb1-8753-4ccd-a0bd-bc0765098f89)



**NAT Gateway**

-   Subnet : Public-Subnet-AZ-1 ,Then Allocate **Elastic IP**

![image](https://github.com/user-attachments/assets/800aba79-cfae-4f83-89ce-cdd088ecce35)



-   Subnet : Public-Subnet-AZ-2 ,Then Allocate **Elastic IP**

![image](https://github.com/user-attachments/assets/ed0a0d75-c4c2-48a4-a4ef-c34395ce9eed)



**Route Table**

-   Created two route tables: PublicRouteTable and Private-RT-AZ1

PublicRouteTable:

Routes

![image](https://github.com/user-attachments/assets/7f48fe97-34e4-49ef-903f-d0847df3c920)



Subnet Association

![image](https://github.com/user-attachments/assets/d4a99bfa-1318-4d12-a255-fdb620ab1a55)


Private-RT-AZ1:

Routes

![image](https://github.com/user-attachments/assets/dc795ce9-f417-4ff0-8b22-9126bd39a3d0)


Subnet Association

![image](https://github.com/user-attachments/assets/218af9d3-3438-4b8f-92f8-103eaec6d7ae)



**Security Group (Created 5 Security Groups)**

1.  internet-facing-lb-sg
2.  WebTierSG
3.  internal-lb-sg
4.  PrivateIntanceSG
5.  DBSG
6.  **internet-facing-lb-sg**:
    -   **Associated with**: Public-facing Application Load Balancer (ALB)
    -   **Inbound Rules**: HTTP (port 80) from My IP
7.  **WebTierSG**:
    -   **Associated with**: EC2 instances running Nginx in the web tier
    -   **Inbound Rules**: HTTP (port 80) from My IP, HTTP (port 80) from internet-facing-lb-sg
8.  **internal-lb-sg**:
    -   **Associated with**: Internal Load Balancer
    -   **Inbound Rules**: HTTP (port 80) from WebTierSG
9.  **PrivateInstanceSG**:
    -   **Associated with**: EC2 instances running Node.js in the application tier
    -   **Inbound Rules**: Custom TCP from My IP ,Custom TCP (port 4000) from internal-lb-sg
10. **DBSG**:
    -   **Associated with**: Aurora MySQL database
    -   **Inbound Rules**: Allow traffic on port 3306 from PrivateInstanceSG

![image](https://github.com/user-attachments/assets/7de10dce-2f93-4171-8239-318de5e367e3)



1.  **Database Deployment**

**Subnet Group Configuration**:

1.  **Name**: three-tier-db-subnet-group
2.  **Description**: Subnet group for the database layer of the architecture
3.  **VPC**: Choose the VPC .

![image](https://github.com/user-attachments/assets/cdf9fdf1-a90a-47d5-862f-a22cb1ffe43e)



**Database Deployment:**

1.  **Navigate to Databases** in the RDS dashboard and click **Create database**.
2.  **Select Standard create** for a MySQL-Compatible Amazon Aurora database. Use default settings.
    -   **Choose Dev/Test** under Templates.
    -   **Set username and password**.
    -   **Create an Aurora Replica** in a different availability zone.
    -   **Set VPC**, choose the subnet group created earlier, and select **no** for public access.
    -   **Assign the security group** created for the database layer and create the database.
3.  **Verify the database** is provisioned with reader and writer instances. Note the writer endpoint.

![image](https://github.com/user-attachments/assets/73b29a29-70e7-4014-80df-2ad5c1a8d782)



1.  **App Tier Instance Deployment**
2.  Navigate to EC2 and click Launch Instances.
3.  Select Amazon Linux 2 AMI and T2.micro instance type (free tier eligible).
4.  Configure Instance Details: Choose the correct Network, private subnet, and attach the IAM role created earlier.
5.  Leave storage settings as default and add a tag with the key as Name and value as AppLayer.
6.  Select the app layer security group created earlier.
7.  Review the instance settings, click Launch, and proceed without a key pair (using Systems Manager).
8.  Launch the instance and verify it in the EC2 dashboard.

![image](https://github.com/user-attachments/assets/d30bb9d5-c70e-4fc6-9443-c549030a832b)



**Connect to Instance**:

1.  **Connect to your instance** using Systems Manager. You'll be logged in as ssm-user by default.
2.  **Switch to ec2-user** by running the command:

sudo -su ec2-user

1.  **Test internet connectivity** by pinging Google's DNS server:

ping 8.8.8.8

Stop the ping by pressing **Ctrl + C** if packets are being transmitted.

![image](https://github.com/user-attachments/assets/b954276f-d39e-4268-8f57-5b586d7a8332)



**Configure Database**:

MySQL should already be installed on the server. Connect to your database using your DB endpoint, username, and the password you set during the database creation.

![image](https://github.com/user-attachments/assets/f90220f9-c5f4-4283-aa00-02a2fbd56fb7)



**Configure App Instance**:

1.  **Update DbConfig.js**: Fill in your database credentials, using the writer endpoint as the hostname and webappdb as the database. Save the file.
2.  **Upload to S3**: Upload the app-tier folder to your S3 bucket.

![image](https://github.com/user-attachments/assets/54734fe4-0343-4014-9710-def13f46efd0)



**4.  Internal Load Balancing and Auto Scaling**

**App Tier AMI**:

1.  In the EC2 dashboard, go to **Instances**. Select the app tier instance, then under **Actions**, choose **Image and templates** and click **Create Image**.
2.  Provide a name and description for the image, then click **Create image**. You can monitor its status by checking **AMIs** under **Images** in the EC2 dashboard.

![image](https://github.com/user-attachments/assets/cafeab0a-fba2-4532-932d-57852becb67b)



**Target Group**:

1.  Go to **Target Groups** under **Load Balancing** in the EC2 dashboard and click **Create Target Group**.
2.  Set the target type to **Instances**, name the group, use HTTP on port 4000, select the VPC, and set the health check path to /health. Click **Next**.
3.  Skip registering targets and create the target group.

![image](https://github.com/user-attachments/assets/f02602e6-a892-4f94-b18e-55b76ca4d8a3)



**Internal Load Balancer**:

1.  Go to **Load Balancers** under **Load Balancing** and click **Create Load Balancer**.
2.  Choose **Application Load Balancer**, name it, and set it to **internal**.
3.  Configure the VPC, select private subnets, apply the internal ALB security group, and set it to listen on HTTP port 80. Forward traffic to the target group you created, then create the load balancer.

![image](https://github.com/user-attachments/assets/2a23d159-1a06-4c73-83d5-f945b2198cf0)


**Launch Template**:

1.  Go to **Launch Templates** under **Instances** and click **Create Launch Template**.
2.  Name the template, select the app tier AMI, and set the instance type to **t2.micro**.
3.  Skip **Key pair** and **Network Settings**. Apply the correct security group and use the same IAM instance profile.

![image](https://github.com/user-attachments/assets/2bd9aaf9-fb0c-4a38-a1a5-d2b0bd459017)



**Auto Scaling**:

1.  Go to **Auto Scaling Groups** and click **Create Auto Scaling Group**.
2.  Name the group and select the previously created Launch Template.
3.  Configure the VPC and private subnets for the app tier.
4.  Attach the Auto Scaling Group to the existing load balancer’s target group.
5.  Set desired, minimum, and maximum capacity to 2, then review and create the Auto Scaling Group.

The Auto Scaling Group will launch 2 app tier instances. To test, manually delete one instance and verify if a new one is created.

![image](https://github.com/user-attachments/assets/87829c0e-c016-4c25-9bc5-495865737d08)



**5. Web Tier Instance Deployment**

**Update Config File**:

1.  Open the application-code/nginx.conf file and replace [INTERNAL-LOADBALANCER-DNS] with your internal load balancer’s DNS entry (found on the load balancer’s details page).
2.  Upload the updated nginx.conf file and the application-code/web-tier folder to your S3 bucket.

![image](https://github.com/user-attachments/assets/90648d1e-4b91-4fc3-93b7-47b50ca17da3)


**Web Instance Deployment**:

1.  Create a new instance similar to the App Tier instance, but this time select a public subnet. Ensure you choose the correct network components, security group, and IAM role.
2.  Auto-assign a public IP on the **Configure Instance Details** page.
3.  Tag the instance with a name for easier identification.

![image](https://github.com/user-attachments/assets/0d10977b-b202-4d2b-893d-c372c4806ee3)



**Web Instance Testing**:

1.  Access your website using the public IP of your web tier instance, found on the EC2 instance details page.
2.  Verify that the website is displaying correctly and that the database is functioning. You should be able to add data.
3.  Be cautious with the delete button as it will clear all entries in your database.

![image](https://github.com/user-attachments/assets/334dc894-1a72-44e0-bda0-034a802f0781)



**Database Page**

![image](https://github.com/user-attachments/assets/4e8a9063-3479-4baf-bef2-eeed9b13d453)


