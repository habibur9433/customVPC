# Custom VPC with Public and Private Subnets
![customVPC](https://github.com/habibur9433/customVPC/assets/136862285/d7294020-d43c-4f48-9759-18a8f199d718)


One of the fundamental components of AWS infrastructure is the Virtual Private Cloud (VPC), a secure and isolated virtual network that allows users to build their customized network environments within the AWS cloud.
In this guide, we will walk you through the process of setting up a custom VPC with two public and two private subnets. The public subnets will host essential components like a bastion host, which provides secure remote access to instances, and a Network Address Translation (NAT) gateway, enabling private instances to access the internet while remaining protected from direct exposure. Furthermore, we'll explore the necessary steps to create instances in both the public and private subnets, ensuring seamless communication and connectivity.

Step 1: VPC Creatio
Go to the AWS Management Console and navigate to the VPC Dashboard.
Click on "Create VPC" and provide the required information:
Name tag: Give a descriptive name for your VPC (e.g., MyCustomVPC).
IPv4 CIDR block: Define the IP range for your VPC. For example, you can use "10.0.0.0/16".

Step 2: Subnet Creation
After creating the VPC, navigate to the "Subnets" section of the VPC Dashboard.
Click on "Create subnet" to create the first public subnet (public1) for the bastion host:
Name tag: Give a descriptive name (e.g., PublicSubnet1).
VPC: Select the VPC you created in Step 1.
Availability Zone: Choose an Availability Zone (AZ) for the first public subnet.
IPv4 CIDR block: Define the IP range for the subnet. For example, "10.0.1.0/24".
Repeat the same steps to create the second public subnet (public2) for the NAT gateway in a different AZ with a different IP range (e.g., "10.0.2.0/24").
Create the first private subnet (private1) in the same way with another IP range (e.g., "10.0.3.0/24") in the first AZ.
Create the second private subnet (private2) with another IP range (e.g., "10.0.4.0/24") in the second AZ.

Step 3: Internet Gateway (IGW) Creation
From the VPC Dashboard, click on "Internet Gateways" in the left-hand menu.
Click on "Create internet gateway" and give it a name (e.g., MyIGW).
Once created, select the IGW and click on "Attach to VPC". Choose the VPC created in Step 1.

Step 4: Route Table Configuration
Go to the "Route Tables" section of the VPC Dashboard.
You should have a default route table for your VPC. Click on "Edit routes".
Add a route to the internet through the Internet Gateway (IGW) you created:
Destination: "0.0.0.0/0".
Target: Select the Internet Gateway (IGW) you created in Step 3.

Step 5: Network ACL (NACL) Configuration
Navigate to the "Network ACLs" section of the VPC Dashboard.
Create a new NACL for public subnets (e.g., PublicNACL) and another for private subnets (e.g., PrivateNACL).
Edit the PublicNACL and configure inbound and outbound rules as follows:
Inbound Rules:
Rule 100: Allow inbound SSH (port 22) traffic from your IP or a specific IP range.
Rule 200: Allow all inbound traffic from the VPC CIDR block (e.g., "10.0.0.0/16").
Rule 300: Deny all inbound traffic (lowest priority).
Outbound Rules:
Rule 100: Allow all outbound traffic to the VPC CIDR block (e.g., "10.0.0.0/16").
Rule 200: Allow all outbound traffic to the internet (0.0.0.0/0).
Rule 300: Deny all outbound traffic (lowest priority).
Edit the PrivateNACL and configure inbound and outbound rules as follows:
Inbound Rules:
Rule 100: Allow inbound traffic from the VPC CIDR block (e.g., "10.0.0.0/16").
Rule 200: Deny all inbound traffic (lowest priority).
Outbound Rules:
Rule 100: Allow all outbound traffic to the VPC CIDR block (e.g., "10.0.0.0/16").
Rule 200: Allow all outbound traffic to the internet via the NAT gateway (0.0.0.0/0).
Rule 300: Deny all outbound traffic (lowest priority).

Step 6: Create Security Groups
Navigate to the "Security Groups" section of the EC2 Dashboard.
Create a new security group for the bastion host (e.g., BastionSG):
Rules: Allow SSH (port 22) access from your IP or a specific IP range.

Step 7: Create Instances
Launch an EC2 instance for the bastion host:
Choose the public1 subnet (PublicSubnet1).
Select the BastionSG security group.
Choose an appropriate Amazon Machine Image (AMI) for the bastion instance.
Configure other settings as needed and launch the instance.
Launch an EC2 instance for the private subnet:
Choose one of the private subnets (e.g., PrivateSubnet1 or PrivateSubnet2).
Select the PrivateNACL and any necessary security groups.
Choose an appropriate AMI for the instance.
Configure other settings as needed and launch the instance.

Step 8: Create NAT Gateway
Navigate to the "NAT Gateways" section of the VPC Dashboard.
Click on "Create NAT Gateway" and provide the required information:
Subnet: Choose public2 subnet (PublicSubnet2) for the NAT gateway.
Elastic IP Allocation: Create a new Elastic IP or select an existing one.

Step 9: Update Route Table for Private Subnets
Go back to the "Route Tables" section of the VPC Dashboard.
Locate the route table associated with the private subnets (PrivateSubnet1 and PrivateSubnet2).
Edit the route and add a route for internet access through the NAT Gateway:
Destination: "0.0.0.0/0".
Target: Choose the NAT Gateway you created in Step 8.

Step 10: Additional Security Configurations
Adjust Security Groups to control inbound and outbound traffic to your instances as required.

Ensure that Network ACLs are properly configured to restrict access to your subnets.
Congratulations! You have successfully set up a custom VPC in AWS with 2 public and 2 private subnets. The public1 subnet hosts a bastion host for secure remote access, and public2 hosts a NAT gateway to allow private instances to access the internet while remaining protected. The private subnet contains an instance that utilizes the NAT gateway for internet access. Additionally, NACLs and Security Groups are in place to control network traffic and provide an additional layer of security for your resources. Your custom VPC is now ready
