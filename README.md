<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# VPC Monitoring with Flow Logs

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-networks-monitoring)

**Author:** siddharthpk nextwork  
**Email:** siddharthpknextwork@gmail.com

---

## VPC Monitoring with Flow Logs

![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-networks-monitoring_3e1e79a1)

---

## Introducing Today's Project!

### What is Amazon VPC?

Amazon VPC lets you create isolated networks in AWS, giving you control over IP ranges, subnets, and security. It's useful for securely deploying resources and managing network traffic in a flexible, customizable environment.


### How I used Amazon VPC in this project

I used Amazon VPC to create isolated networks, set up subnets, and configure VPC peering for communication between the two VPCs. I also enabled VPC Flow Logs to track network traffic and analyzed the data using CloudWatch Logs Insights.


### One thing I didn't expect in this project was...

One thing I didn’t expect was the level of detail in the flow logs and how much they could reveal about traffic patterns, like the source of the traffic being EC2 Instance Connect. It really helped pinpoint issues and troubleshoot the network setup!


### This project took me...

This project took me about 2-3 hours to complete, from setting up the VPCs and instances to configuring the flow logs and analyzing the traffic. It was a solid hands-on learning experience with a lot of steps to keep track of!


---

## In the first part of my project...

### Step 1 - Set up VPCs


Today, we'll set up two VPCs, peer them, enable Flow Logs to capture network traffic, and analyze that traffic using CloudWatch Log Insights.


### Step 2 - Launch EC2 instances

In this step, I’m going to launch an EC2 instance in each VPC. This will allow us to test the VPC peering connection by enabling communication between these instances. Once they’re running, we can verify that the peering is working as expected.


### Step 3 - Set up Logs

In this step, I’m going to enable VPC Flow Logs to track all inbound and outbound network traffic, and set up CloudWatch Logs to store and manage these records for easy access and analysis.


### Step 4 - Set IAM permissions for Logs

In this step, I’m going to finish setting up the subnet’s flow log by giving it permission to send log data to CloudWatch. This is important so the flow logs can be stored and viewed for traffic analysis and troubleshooting.


---

## Multi-VPC Architecture

I started my project by launching 2 VPCs. In each VPC, I created 1 public subnet and 0 private subnets to allow basic network communication for testing the VPC peering connection.


The CIDR blocks for VPCs 1 and 2 are unique. They have to be unique because overlapping IP ranges would prevent proper routing between the VPCs, causing conflicts and making the peering connection fail.


### I also launched EC2 instances in each subnet

My EC2 instances' security groups allow all ICMP traffic from `0.0.0.0/0`. This is because we want to test the VPC peering connection by sending ping requests (ICMP) between the instances.

![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-networks-monitoring_e7fa8775)

---

## Logs

Logs are records of network traffic or system activities. VPC Flow Logs capture details about inbound and outbound traffic, helping with monitoring and troubleshooting.


Log groups are containers in CloudWatch Logs that organize and store log streams. They allow you to manage, monitor, and analyze logs from different sources, like VPC Flow Logs, in a centralized location.


### I also set up a flow log for VPC 1

![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-networks-monitoring_e8398869)

---

## IAM Policy and Roles

I created an IAM policy because VPC Flow Logs need permission to send log data to CloudWatch. This policy allows the necessary actions like creating log groups and streams, and writing log events, so the logs can be stored and accessed.


I also created an IAM role because VPC Flow Logs need a role to assume that has the right permissions. This role allows the service to use the IAM policy to send log data to CloudWatch on my behalf.


A custom trust policy is a set of rules that defines which services or users are allowed to assume a specific IAM role. It controls the trust relationship between the role and the service that needs to use it, like VPC Flow Logs.


![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-networks-monitoring_4334d777)

---

## In the second part of my project...

### Step 5 - Ping testing and troubleshooting

In this step, I’m going to send test messages from Instance 1 to Instance 2 to verify that the VPC peering connection is working correctly. This helps confirm that the two instances can communicate across VPCs.


### Step 6 - Set up a peering connection

In this step, I’m going to create a VPC peering connection between my two VPCs. This link will allow them to communicate using their private IP addresses, which is essential for secure and direct traffic between the instances.


### Step 7 - Analyze flow logs

In this step, I’m going to check VPC 1’s flow logs to analyze network traffic and confirm that the peering connection and security settings are working correctly.


---

## Connectivity troubleshooting

My first ping test between my EC2 instances had no replies, which means the instances couldn't communicate. This could be due to missing routes, incorrect security group rules, or issues with the VPC peering connection.


![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-networks-monitoring_99d4ba42)

I could receive ping replies if I ran the ping test using the other instance's public IP address, which means the instances can communicate over the internet, but it doesn’t confirm that the VPC peering connection is working.


---

## Connectivity troubleshooting

Looking at VPC 1's route table, I identified that the ping test with Instance 2's private address failed because there’s no route to VPC 2’s CIDR block. The traffic defaults to the internet gateway instead of using the VPC peering connection.


### To solve this, I set up a peering connection between my VPCs

I also updated both VPCs' route tables so that traffic destined for the other VPC’s CIDR block is directed through the VPC peering connection. This ensures private communication between instances in each VPC.


![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-networks-monitoring_7316a13d)

---

## Connectivity troubleshooting

I received ping replies from Instance 2's private IP address! This means the peering connection is active, the route tables are correctly updated, and both instances can now communicate privately across VPCs without using the public internet.


![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-networks-monitoring_4ec7821f)

---

## Analyzing flow logs

Flow logs tell us about network traffic, including who sent it (source IP), who received it (destination IP), ports used, protocol (like TCP), how much data was sent, if it was allowed or denied, and when it happened.


For example, the flow log I've captured tells us that traffic from a specific source IP to a destination IP on a certain port was allowed, using the TCP protocol. It shows how much data was sent, how many packets, and when the traffic occurred.


![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-networks-monitoring_d116818e)

---

## Logs Insights

Logs Insights is a tool in CloudWatch that lets you search, analyze, and visualize log data using queries. It helps you quickly find patterns, troubleshoot issues, and gain insights from logs like VPC Flow Logs.


I ran the query `fields @timestamp, srcAddr, destAddr, bytes | stats sum(bytes) by srcAddr, destAddr | sort sum(bytes) desc | limit 10`. This query analyzes the top 10 byte transfers by source and destination IP addresses, helping identify the heavie

![Image](http://learn.nextwork.org/mischievous_red_fierce_pony/uploads/aws-networks-monitoring_3e1e79a1)

---

---
