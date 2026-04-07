 AWS Cloud Infrastructure Project

Built by ILANTHIRAYAN R — AWS Cloud Developer
A comprehensive AWS project demonstrating real-world cloud infrastructure, event-driven automation, and scalable web hosting.

## Project Overview ##
This project showcases a multi-service AWS cloud infrastructure that integrates:

EC2 Instance Management with state-change monitoring
EventBridge Rules for automated event-driven workflows
SNS (Simple Notification Service) for real-time email alerts
Auto Scaling Groups for dynamic instance management
Elastic Beanstalk for managed web application deployment
S3 Buckets for static website and asset hosting
IAM Roles & Security Groups for secure access control
Portfolio Website hosted on AWS infrastructure


## Architecture Diagram ##
                         ┌─────────────────────────────────────┐
                         │           AWS Cloud (us-east-1)      │
                         │                                       │
  User/Browser ─────────►│  Elastic Beanstalk (Myscale-env)     │
                         │  └── EC2 t2.micro (Tomcat 11)        │
                         │                                       │
                         │  EC2 Auto Scaling Group              │
                         │  ├── Min: 2 instances                │
                         │  ├── Max: 4 instances                │
                         │  └── Launch Template: "sample"       │
                         │                                       │
  Email Alerts ◄─────────│  SNS Topic: my-topic                 │
                         │  └── Subscriptions (Email)           │
                         │                                       │
                         │  EventBridge Rules                   │
                         │  ├── sri-rule (Enabled)              │
                         │  └── therole (Enabled)               │
                         │                                       │
                         │  S3 Buckets                          │
                         │  ├── elasticbeanstalk-us-east-1-...  │
                         │  ├── jeganmad                        │
                         │  └── mybuket-182                     │
                         │                                       │
                         │  IAM Role: hellouser                 │
                         │  Security Group: new security        │
                         │  └── HTTP (80), SSH (22)             │
                         └─────────────────────────────────────┘

## AWS Services Used ##
ServicePurposeConfigurationEC2Virtual servert2.micro, us-east-1a, AMI: ami-00a929b66ed6e0de6Auto ScalingDynamic scalingMin: 2, Max: 4, Desired: 2EventBridgeEvent routingRules: sri-rule, therole (both Enabled)SNSNotificationsTopic: my-topic, Email subscriptionsElastic BeanstalkApp deploymentTomcat 11, Corretto 21, Amazon Linux 2023S3Object storage3 buckets in us-east-1IAMAccess controlRole: hellouser, AWSElasticBeanstalkCustomPlatform policySecurity GroupsNetwork securityInbound: HTTP/80, SSH/22VPCNetwork isolationvpc-0ca038acd5ee74cf2

## Project Modules ##
Module 1: EC2 Instance + SNS Notifications
What it does:
Monitors EC2 instance state changes (pending → running → stopped) and sends real-time email alerts via SNS.
Components:

EC2 Instance: i-0d678c1db76b1bf00 (linux, t2.micro)
SNS Topic: arn:aws:sns:us-east-1:610694133212:my-topic
Email subscriptions confirmed for multiple recipients

#Steps to replicate:#

Launch an EC2 instance (t2.micro, Amazon Linux)
Create an SNS topic → Add email subscriptions → Confirm via email
Create an EventBridge rule targeting EC2 state changes
Set the SNS topic as the EventBridge rule target
Start/Stop the EC2 instance and verify email notifications


#Module 2: EventBridge Rules#
What it does:
Two EventBridge rules (sri-rule, therole) watch for EC2 state-change events on the default event bus and route them to SNS targets.
Steps to replicate:

Go to Amazon EventBridge → Rules → Create rule
Event source: AWS services → EC2 → EC2 Instance State-change Notification
Target: SNS topic (my-topic)
Enable the rule


#Module 3: Auto Scaling Group#
What it does:
Automatically launches and terminates EC2 instances based on demand. Configured with a Launch Template and scaling limits.
Configuration:

Group name: auto new
Launch template: sample (lt-0e0a43b19e3be6220)
AMI: ami-00a929b66ed6e0de6
Instance type: t2.micro
Security group: sg-0bfba23a985de7437
Desired: 2 | Min: 2 | Max: 4

Steps to replicate:

Create a Launch Template with your AMI, instance type, and security group
Go to EC2 → Auto Scaling Groups → Create
Attach the launch template
Set desired/min/max capacity
(Optional) Add scaling policies based on CPU or custom metrics


Module 4: Elastic Beanstalk Deployment
What it does:
Deploys a Java web application on a managed Elastic Beanstalk environment with Tomcat 11.
Environment: Myscale-env
Domain: simple.us-east-1.elasticbeanstalk.com
Platform: Tomcat 11 with Corretto 21 on 64-bit Amazon Linux 2023/5.6.0
IAM Role: hellouser (with AWSElasticBeanstalkCustomPlatform policy)
Steps to replicate:

Go to Elastic Beanstalk → Create Application
Choose platform: Tomcat (Java)
Upload your .war or .zip file
Create an IAM service role with Elastic Beanstalk permissions
Launch the environment and access via the provided domain


Module 5: Portfolio Website on AWS
What it does:
A personal portfolio website for ILANTHIRAYAN R (AWS Cloud Developer) created with HTML/CSS and deployed on AWS infrastructure. Includes a downloadable resume and a QR code.
Tech stack:

HTML5, CSS3, Bootstrap
Font Awesome icons
Hosted via Elastic Beanstalk / S3

Key sections: Profile, AWS Skills, Technical Skills, Portfolio projects

Module 6: S3 Buckets
Three general-purpose S3 buckets in us-east-1:
Bucket NameCreatedPurposeelasticbeanstalk-us-east-1-610694133212Mar 20, 2025Beanstalk deployment artifactsjeganmadMar 21, 2025General storagemybuket-182Mar 31, 2025Static assets / website files

## Setup & Deployment Guide ##
Prerequisites

AWS Account (Free Tier eligible)
AWS CLI installed and configured
IAM user with appropriate permissions
Git installed locally

Step 1 — Clone this repository
bashgit clone https://github.com/YOUR_USERNAME/aws-cloud-project.git
cd aws-cloud-project
Step 2 — Configure AWS CLI
bashaws configure
# Enter: Access Key ID, Secret Access Key, Region (us-east-1), Output format (json)
Step 3 — Deploy EC2 + SNS Notifications
bash# Create SNS topic
aws sns create-topic --name my-topic

# Subscribe your email
aws sns subscribe \
  --topic-arn arn:aws:sns:us-east-1:YOUR_ACCOUNT_ID:my-topic \
  --protocol email \
  --notification-endpoint your@email.com

# Create EventBridge rule
aws events put-rule \
  --name "ec2-state-change-rule" \
  --event-pattern '{"source":["aws.ec2"],"detail-type":["EC2 Instance State-change Notification"]}' \
  --state ENABLED
Step 4 — Create Auto Scaling Group
bash# Create launch template
aws ec2 create-launch-template \
  --launch-template-name sample \
  --launch-template-data '{"ImageId":"ami-00a929b66ed6e0de6","InstanceType":"t2.micro"}'

# Create Auto Scaling Group
aws autoscaling create-auto-scaling-group \
  --auto-scaling-group-name "auto-new" \
  --launch-template LaunchTemplateName=sample,Version='$Default' \
  --min-size 2 --max-size 4 --desired-capacity 2 \
  --availability-zones us-east-1a us-east-1b
Step 5 — Deploy to Elastic Beanstalk
bash# Initialize Beanstalk app
eb init myscale --region us-east-1 --platform "Tomcat 11 with Corretto 21"

# Create environment
eb create myscale-env

# Deploy
eb deploy
Step 6 — Push to GitHub
bashgit add .
git commit -m "Initial AWS cloud infrastructure project"
git push origin main

## Screenshots ##
FeatureDescriptionEC2 InstanceLinux instance (t2.micro) in us-east-1a, Stopped state after EventBridge triggerSNS Subscription ConfirmationEmail confirmation received for my-topicSNS Stop NotificationJSON payload showing EC2 state change to "stopped"EventBridge RulesTwo enabled rules: sri-rule and theroleSNS Subscriptions3 subscriptions (2 confirmed, 1 pending)Auto Scaling Result6 instances launched (1/6 visible in EC2 console)Auto Scaling Group"auto new" group with desired:2, min:2, max:4Launch TemplatesTwo templates: "sample" and "jegantemp"Security Group"new security" — HTTP:80 + SSH:22 inbound rulesElastic Beanstalk EnvMyscale-env with Warning health stateBeanstalk Live URLCongratulations page at simple.us-east-1.elasticbeanstalk.comIAM Rolehellouser role with AWSElasticBeanstalkCustomPlatform policyPortfolio HTML Codeindex.html with Bootstrap, Font AwesomeResume HTML CodeXHTML strict resume for ILANTHIRAYAN RPortfolio PreviewLive resume page with Download PDF and skillsQR CodeQR code linking to portfolio/resumeS3 Buckets3 general-purpose buckets in us-east-1

 ##Skills Demonstrated##

✅ EC2 lifecycle management
✅ Event-driven architecture with EventBridge + SNS
✅ Auto Scaling for high availability
✅ Elastic Beanstalk PaaS deployment
✅ IAM roles and least-privilege access
✅ Security Group configuration (HTTP + SSH)
✅ S3 bucket management
✅ HTML/CSS portfolio development
✅ AWS CLI usage
✅ Multi-service integration
