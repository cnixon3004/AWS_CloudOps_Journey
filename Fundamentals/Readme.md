# Project: Getting Hands-On with Amazon VPC (Nextwork.org) 

## 📌 Project Overview
Hands-on networking project built during my AWS SysOps (SOA-C03) certification studies — applying concepts from the Solutions Architect certification in real AWS environments.

**Objective:** Build a fully functional Amazon VPC environment that includes both public and private subnets. Implement Network ACLs and Security Groups to define precise inbpund and outbound traffic rules. Enable VPC Flow Logs to monitor Layer 3 network activity for visibility and troubleshooting. Finally, configure VPC Endpoints to allow secure, private access to AWS services without exposing resources to the public internet.

## 🛠 AWS Services Used
*   **VPC:** Custom subnets, Route Tables, and Internet Gateway (IGW).
*   **EC2:** Hosting web and database instances.
*   **Security:** Security Groups and Network ACLs.
*   **Monitoring:** VPC Flow Logs and CloudWatch.

*   ## 🏗 Architecture & Key Highlights
*   **Accomplishment:** Built a dual-stack VPC environment with isolated private subnets.
*   **Key Skill:** Set up route tables and linked them to the right subnets to control how traffic flows in the VPC. Used Security Groups to manage access at the instance level, and           Network ACLs to add an extra layer of control at the subnet level.

![VPC Resource Map](/Fundamentals/Images/Resource_Map.png)
 
*Bird's-eye View of the network architecture.*

## 🎯 Next Steps
- Implement NAT Gateways and private route configurations  
- Explore VPC Endpoints and Gateway configurations  
- Monitor traffic with CloudWatch and Flow Logs analytics

