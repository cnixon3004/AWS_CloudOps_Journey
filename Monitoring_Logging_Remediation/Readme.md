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


2. SNS Configuration & CloudWatch Alarm setup

   Next I setup SNS by
      * Creating a SNS topic
      * Subsricbed an email endpoint for notification
   <p float="left">
    <img src="/Monitoring_Logging_Remediation/Screenshots/CW_SNS.png" width="45%" />
    <img src="/Monitoring_Logging_Remediation/Screenshots/topic_confirmation.png" width="45%" />
   </p> 
   *Create Topic & Topic Email confirmation*

Once the topic was created I then navigated back to CloudWatch and created an alarm that would trigger a notification if CPUUtilization went over 70% usage 
![CloudWatch Alarm Preview](/Monitoring_Logging_Remediation/Screenshots/CW_alarm_preview1.png)
*CloudWatch Alarm Preview*

3. Testing

   After all of that the only thing left to do was to test the instance/alarm in a stressed state
   I did this by installing a stress tool and then enetering a command to potentially max out the cpu of the instance.
   ![Stress Tool Installation](/Monitoring_Logging_Remediation/Screenshots/Stress_test.png)
   *Stress Test Install*

   ![Simulated Stress Load](/Monitoring_Logging_Remediation/Screenshots/Stress_load2.png)

   *Simulated load on EC2 Instance*
   
   Once alarm crossed both datapoints a email notification was sent to the email attached to the notification
      <p float="left">
    <img src="/Monitoring_Logging_Remediation/Screenshots/alarm_email.png" width="45%" />
    <img src="/Monitoring_Logging_Remediation/Screenshots/alarm_state.png" width="45%" />
   </p> 
  
   *CloudWatch Alarm Email*

   *Visualization of Alarm State*
   
   
