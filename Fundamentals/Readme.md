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
* Route Tables direct subnet traffic, while Security Groups and NACLs provide layered security.
* Separation of resources (public vs private) lays the groundwork for scalability and security.

![VPC Resource Map](/Fundamentals/Images/Resource_Map.png)
 
*Bird's-eye View of the network architecture.*

## 🚀 Part 2: Launching VPC Resources & Connectivity

### 🧩 Objectives
- Launch VPC Resources
- Test VPC Connectivity

Highlights

Deployed two t2.nano instances — one public (bastion) and one private.

Used SG chaining so the private instance only accepts traffic from the public instance.

Troubleshot connectivity issues (missing ICMP rules) and confirmed successful internal communication.

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

<p align="center">
  <img src="/Fundamentals/Images/logGroupCreate.png" alt="New Log Group" width="45%" />
  <img src="/Fundamentals/Images/NewFlowLog.png" alt="New Flow Log" width="45%" />
</p>
*New Log Group and Flow Log*
Since there was no existing IAM role for Flow Logs, I created one manually:

🧠 IAM Setup

Create Policy

Allowed:

logs:CreateLogGroup

logs:CreateLogStream

logs:PutLogEvents

![Flow Role](/Fundamentals/Images/NewPolicy.png)
*Log Policy* 

Create Role

Attached the new policy to a role for Flow Logs.

Once the role was attached to the Flow Log, traffic to and from the Test1 VPC began streaming into CloudWatch.

🔍 Step 4: Testing and Analyzing the Flow Logs

To validate that everything worked, I pinged the Test2 instance from Test1 and analyzed the logs in CloudWatch Logs Insights.

Using simple queries, I could see accepted/denied traffic between both private IPs — providing visibility into the network behavior.

![VPC Insights](/Fundamentals/Images/loginsights.png)
*Default query for Log Insights*

🧠 What I Learned

This lab reinforced how networking, IAM, and monitoring all tie together in AWS.
Understanding these concepts helped me see how real-world architectures are built and observed — not just deployed.


# 🚀 **Part 4: Connecting EC2 to S3 (Direct & via VPC Endpoint)**

*(Final stage of the Nextwork Networking Lab – learning to securely connect private resources to AWS services)*

---

### 🧠 **Overview**

With my VPC architecture and security layers complete, the next challenge was to connect an EC2 instance to **Amazon S3** — first *over the internet* using access keys, and then *privately* through a **VPC Endpoint**.

This lab helped me understand not just *how* connectivity works, but *why* security and access design matter in cloud networking.

---

### ☁️ **Step 1: Accessing S3 Through the Internet**

To start, I spun up my EC2 instance in the **Test1 VPC** and stripped the Security Group down to just one inbound rule — **SSH (port 22)** from anywhere.
Once connected through **EC2 Instance Connect**, I tried listing all buckets:

```bash
aws s3 ls
```

🔴 *Result:* `Unable to locate credentials`

That message reminded me that the CLI needs credentials, so I ran:

```bash
aws configure
```

The terminal prompted me for the **Access Key ID** and **Secret Access Key**.
To create these, I went to:
**IAM → Users → (Select user) → Security Credentials → Create access key**.

After adding the new credentials in the terminal, I re-ran:

```bash
aws s3 ls
```

✅ *Result:* All buckets in that region listed successfully.

To take it further, I created a new bucket and uploaded a test file directly from EC2:

```bash
aws s3 mb s3://nextwork-lab-bucket
touch test.txt
aws s3 cp test.txt s3://nextwork-lab-bucket
```

Then I verified everything:

```bash
aws s3 ls
aws s3 ls s3://nextwork-lab-bucket
```

💡 *Seeing my test file show up in the console was a big win — it confirmed I could manage S3 directly from the command line.*

---

### 🖼️ **Recommended Screenshots**

* IAM → Access Key creation screen
* EC2 Instance Connect terminal showing `aws configure` prompts
* Successful `aws s3 ls` output
* Console view confirming uploaded file in S3 bucket

![IAM Access Key](/Fundamentals/Images/iam-accesskey.png)
![EC2 CLI Configure](/Fundamentals/Images/ec2-configure.png)
![S3 Bucket List](/Fundamentals/Images/s3-list.png)
![S3 Console Verification](/Fundamentals/Images/s3-console.png)

---

### 🔒 **Step 2: Connecting to S3 via VPC Endpoint**

After verifying connectivity over the internet, I wanted to take a more secure approach — connecting privately through a **VPC Gateway Endpoint for S3**.

I first deleted the test bucket and created a fresh one with several uploaded files for testing.
Next, I navigated to:
**VPC → Endpoints → Create Endpoint → Gateway → S3**

Once the endpoint was active, I wrote a **bucket policy** to restrict access to traffic *only* coming from that specific endpoint.
When I saved it, I got a few **Access Denied** messages — which actually made sense. The new policy was doing its job: blocking all traffic that wasn’t from the endpoint.

To fix routing, I updated my **Route Table** for the subnet to include the new **VPC endpoint** as a target for S3 traffic.
Then I tested again:

```bash
aws s3 ls
```

✅ *Result:* Success — this time, traffic stayed entirely within AWS’s private network instead of traversing the public internet.

---

### 🖼️ **Recommended Screenshots**

* VPC → Endpoint creation (Gateway type, Service: com.amazonaws.us-east-1.s3)
* Bucket policy JSON snippet showing VPC endpoint restriction
* Route Table with endpoint target
* Terminal showing successful S3 access via endpoint

![VPC Endpoint Creation](/Fundamentals/Images/vpc-endpoint.png)
![Bucket Policy](/Fundamentals/Images/bucket-policy.png)
![Route Table Endpoint](/Fundamentals/Images/route-endpoint.png)
![S3 Access via Endpoint](/Fundamentals/Images/s3-endpoint-access.png)

---

### 🧩 **Key Takeaways**

* Using **Access Keys** works but exposes communication to the public internet — better suited for testing or learning.
* **VPC Endpoints** enable private, secure communication between AWS resources and services without ever leaving the AWS network.
* Understanding IAM roles and S3 bucket policies is crucial for secure design.
* Hands-on troubleshooting (like fixing route tables or endpoint permissions) made these concepts *click* in a way that reading alone never could.

---

### 🧠 **Reflection**

This lab wrapped up my AWS networking fundamentals on a high note.
Each part built on the last — starting with subnet basics, moving through connectivity, monitoring, and now private service access.
Seeing everything come together made the “why” behind AWS architecture start to make sense.

Next up: Automating the entire environment with **AWS CloudFormation** 🚀




 



  



