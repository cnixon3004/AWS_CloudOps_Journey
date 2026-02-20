# Project: Getting Hands-On with Amazon VPC (Nextwork.org) 

## 📌 Project Overview
Hands-on networking project built during my AWS SysOps (SOA-C03) certification studies — applying concepts from the Solutions Architect certification in real AWS environments.

**Objective:** Build a fully functional Amazon VPC environment that includes both public and private subnets. Implement Network ACLs and Security Groups to define precise inbound and outbound traffic rules. Enable VPC Flow Logs to monitor Layer 3 network activity for visibility and troubleshooting. Finally, configure VPC Endpoints to allow secure, private access to AWS services without exposing resources to the public internet.

## 🛠 AWS Services Used
*   **VPC:** Custom subnets, Route Tables, and Internet Gateway (IGW).
*   **EC2:** Hosting web and database instances.
*   **Security:** Security Groups and Network ACLs.
*   **Monitoring:** VPC Flow Logs and CloudWatch.

*   ## 🏗 Architecture & Key Highlights
*   ## Part 1: Creating VPC and Subnets
*   ### 🧩 Objective
    - Build a Vitural Private Cloud
    - VPC Security
    - Create a Private Subnet
*   ### 🔍 Key Takeaways
*  I set up route tables and associated them with the appropriate subnets to define traffic flow. Security Groups handled access at the instance level, while Network ACLs added subnet-level control.

![VPC Resource Map](/Fundamentals/Images/Resource_Map.png)
 
*Bird's-eye View of the network architecture.*

## 🚀 Part 2: Launching VPC Resources & Connectivity

### 🧩 Objectives
- Launch VPC Resources
- Test VPC Connectivity

### 🔍 Key Takeaways
After setting up the network, I launched two t2.nano instances — one in the public subnet and one in the private subnet.

-The public instance acted as a bastion host, with SSH access controlled via key pair and Security Group rules.
-The private instance was reachable only through traffic originating from the public instance’s Security Group (SG chaining).
<p float="left">
  <img src="/Fundamentals/Images/privateinst.png" width="45%" />
  <img src="/Fundamentals/Images/pubinst.png" width="45%" />
</p> 
Side by side of view of both instance.


![Public SG chained to Private instance](/Fundamentals/Images/Prisg.png)
*Security Group rules for the Private Instance.*

During testing:
Public Instance: Verified external internet access (ping + curl successful)
![Public Test](/Fundamentals/Images/pubtest.png)
*Ping and -curl successfull*

Private Instance: Initial pings failed — after investigation, found missing outbound ICMP rules in its Security Group.
![Ping Failed](/Fundamentals/Images/privateping.png)<br>
*Communication with Private Instance Failed.*

After investigation, found missing outbound ICMP rules in its Security Group. Once corrected, both instances communicated successfully, confirming correct ACL, SG, and route configurations.
![New Private Security Group](/Fundamentals/Images/newprisg.png)
*Edited Private Security Group*<br>

Now the two public and private instances can communicate
![Successfull Pings](/Fundamentals/Images/Pub_Pri_success.png) <br>
*Success*

🚀 Next Steps
* VPC Peering
* VPC Monitoring with Flow Logs
* Accessing S3 from VPC
* VPC Endpoints
  



