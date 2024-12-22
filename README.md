# DevOps-Tooling-Website-Solution

## Table of Contents
1.  Introduction
      - Project Overview
     - Self-Study: Storage Concepts
2.  Prerequisites
3.  Architecture Overview
4.  Setting Up AWS EC2 Instances
5.  Configuring NFS Server
    - EBS Setup
    - LVM Configuration
    - Install and Configure NFS server
6.  Configuring Database Server
    - EBS Setup
    - LVM Configuration
    - AWS Security Group Configuration
    - Install and Configure MySQL Database Server
7.  Configuring Web Servers
    - Installing NFS Client
    - Mounting NFS Shares
    - Installing PHP and extensions
8.  Deploying Tooling Website
9.  Final Steps and Reflections


# Introduction

## Project Overview
This project is an extension of my previous work, where I deployed a scalable WordPress application using a 3-tier architecture on AWS with EBS volumes and LVM for dynamic storage management. In this iteration,I'm enhancing the scalability and laying the foundation for a more robust DevOps tooling solution by implementing the following key changes:

  - Centralized File Storage: I'm introducing a Network File System (NFS) server to provide centralized, shared storage. This allows me to run multiple web servers efficiently, improving our application's scalability and performance.
  - Multiple Web Servers: Instead of a single web server, I'm now deploying multiple web servers that will connect to the centralized NFS storage. This setup enhances the ability to handle increased traffic and provides better fault tolerance.
  - Dedicated Database Server: We maintain a separate database server, continuing the practice of separating concerns in the architecture.
  - DevOps Tooling Focus: While the previous project centered on WordPress, this solution is geared towards hosting DevOps tools. This shift in focus prepares the infrastructure for hosting various DevOps-related applications and utilities.
  - Enhanced Scalability: By separating file storage from the web servers and using NFS, I'm creating a more flexible system that can easily scale by adding more web servers as needed.
  - Continued Use of LVM: I'm still leveraging Logical Volume Manager (LVM) for dynamic storage management, applying this to both the NFS server and database server for flexible disk space allocation.

The goal of this project is to create a scalable and maintainable infrastructure that can serve as a solid foundation for various DevOps tools and practices. By building on the previous work and introducing new elements like NFS, I'm moving towards a more robust, enterprise-ready solution that can adapt to growing demands and complexities in a DevOps environment.

Self-Study: Storage Concepts
Below afre the concepts learnt during my self-study to have a general idea of how the varous technologies relates and theur use cases:

Network Storage Types:

NAS (Network Attached Storage): File-level storage for easy file sharing in smaller environments, using protocols like NFS or SMB/CIFS.
SAN (Storage Area Network): Block-level storage on a dedicated network, ideal for high-performance applications, using protocols like Fibre Channel or iSCSI.
Data Storage Paradigms:

Block Storage: Fixed-size data blocks with unique addresses, suitable for OS and databases, offering low-latency I/O (e.g., Amazon EBS).
Object Storage: Data stored as objects with metadata, highly scalable for unstructured data like media files (e.g., Amazon S3).
Network File System (NFS): File-level protocol for remote file access, allowing shared access across multiple clients.
AWS Cloud Storage Services:

Amazon EBS: Block-level storage volumes for EC2 instances.
Amazon S3: Scalable object storage for various data types.
Amazon EFS: Managed NFS for EC2, providing scalable file storage.
These storage concepts form the foundation for understanding the infrastructure choices in our DevOps tooling project, particularly the use of NFS for shared storage across multiple web servers.

# Prerequisites
Before beginning this project, ensure you have the following:

## 1. AWS Account:
An active AWS account with permissions to create and manage EC2 instances, EBS volumes, and security groups.
  
##  2. Knowledge Base:
  -Basic understanding of Linux command line
  -Familiarity with AWS services, particularly EC2 and EBS
  -Basic networking concepts

##  3.  Tools:
SSH client (e.g., PuTTY for Windows or Terminal for MacOS/Linux)
Git installed on your local machine


  ## 4.  Security: 
Create or use an existing EC2 Key Pair for SSH access
Browser: A modern web browser for accessing the AWS Management Console


## 5.  Browser: A modern web browser for accessing the AWS Management Console

##   Architecture Overview
Below is a diagram illustrating the architecture of our DevOps Tooling Website Solution:

![image](https://github.com/user-attachments/assets/84e7b4ce-05e6-4632-b837-14c5dd578612)

This architecture provides:

  - Scalability through multiple web servers
  - Centralized file storage via NFS
  - Separated database for better resource management

# Setting Up AWS EC2 Instances

Follow these steps to set up the required EC2 instances:

   1.  Launch NFS Server:

       - Navigate to EC2 dashboard in AWS Console
       - Click "Launch Instance"
       - Choose "Red Hat Enterprise Linux 9.4" AMI
       - Select t2.small instance type
       - Configure instance details (VPC, subnet, etc.)
       - Add 3 x 15GB EBS volumes
       - Configure security group (will be detailed later)
       - Review and launch with your EC2 key pair
    2.  Launch Web Servers (Repeat 3 times):

       - Follow similar steps as NFS server
       - Choose t2.small instance type
       - No additional EBS volumes required
     3.  Launch Database Server:
       - Follow similar steps as NFS server
       - Choose t2.micro instance type
       - Add 3 x 10GB EBS volumes

     4.  Verify Instances:

  Ensure all instances are in the "running" state
Note down the private IP addresses of all instances
Confirm that all instances are in the same subnet (for this project)
Tag Instances:

Add descriptive tags to each instance (e.g., "NFS Server", "Web Server 1", "DB Server")
Consider adding a tag to indicate that this is a learning/development environment
Configure Elastic IPs (Optional):

Allocate and associate Elastic IPs to web servers if public access is required


