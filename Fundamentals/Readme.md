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

## 🚀 Part 3: VPC Peering and Monitoring with Flow Logs 

### 🧩 Objectives
- Setup VPC Peering Connection    
- Monitor commnunication between VPC's using Flow Logs

### 🔍 Key Takeaways
* VPC Peering enables private network connectivity without public internet routing.

* Flow Logs help visualize and troubleshoot network behavior between resources.

* IAM roles and policies play a critical part in granting permissions for AWS services to communicate securely.
    * Policies are the rules that determine what someone/something can or cannot do
    * Roles are the policies and permissions bundled to give services the permissions needed to complete the neccessary tasks
 
---

## 🏗️ Step 1: Creating the Second VPC

After building my first VPC in a previous lab, I wanted to take it further by connecting two separate networks privately using **VPC Peering**.

I started by creating a **second VPC (`Test2`)** with a setup similar to my first one:
- Subnet
- Route Table
- Network ACL
- Security Group

Then I launched an **EC2 instance** inside the public subnet using the same SG settings as the first VPC — because repetition is the best teacher 😄.

✅ **Test:** Verified that the instance in `Test2` could reach the internet using simple commands:

```bash
ping 8.8.8.8
curl https://example.com
```
![Test2 Successfull Communication](/Fundamentals/Images/pub2test.png)
*Success*

🔗 Step 2: Establishing the VPC Peering Connection

Next, I created a VPC Peering Connection between Test1 and Test2:

AWS Console → VPC → Peering Connections → Create Peering Connection

After sending the request from Test1, I switched to Test2 and accepted the request.
Once the connection became Active, I updated both route tables so each VPC knew how to reach the other’s CIDR block through the peering connection.

✅ Test: Connected to the instance in Test1 and successfully pinged the private IP of the instance in Test2.

![Accept Peering Request](/Fundamentals/Images/peeringrequest.png)

*Accept Peering Request to continue*

![Communication is a success](/Fundamentals/Images/T1_T2.png)

*Test 1 pinging Test 2*

📊 Step 3: Enabling VPC Flow Logs for Monitoring

With both VPCs connected, I wanted to start monitoring traffic between them using Flow Logs.

I went to CloudWatch → Logs → Log groups and created a log group named TestLog.

Then, from the Test1 VPC, I created a new Flow Log:

Filter: All traffic

Aggregation interval: 1 minute

Destination: CloudWatch Log Group (TestLog)




 



  



