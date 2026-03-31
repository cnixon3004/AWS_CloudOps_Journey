AWS CloudWatch + SNS Monitoring Lab
📌 Overview

This project demonstrates how to monitor EC2 instance performance using Amazon CloudWatch and trigger automated alerts using Amazon SNS.

The goal of this lab is to simulate real-world monitoring by detecting high CPU utilization and sending notifications for proactive response.

🏗️ Architecture

EC2 Instance → CloudWatch Metrics → CloudWatch Alarm → SNS → Email Notification

⚙️ Services Used
 * Amazon EC2
 * Amazon CloudWatch
 * Amazon SNS

🚀 Implementation Steps
1. EC2 & CloudWatch Setup

   Began by creating and launching a t2.nano instance. Once the instance was up and running I navigated over to CloudWatch to verify that the default metrics weere being collected.

                   Cloudwatch >> Metrics >> EC2 >> Per-Instance Metrics >> your_instance_ID
    ![CPU Metrics](/Monitoring_Logging_Remediation/Screenshots/CW_cpumetrics.png)
   *CPU Utilization Instance Metrics*
   
