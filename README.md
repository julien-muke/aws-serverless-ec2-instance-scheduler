# ![aws](https://github.com/julien-muke/Search-Engine-Website-using-AWS/assets/110755734/01cd6124-8014-4baa-a5fe-bd227844d263) AWS serverless system to automatically turn EC2 instances ON and OFF.


## <a name="introduction">🤖 Introduction</a>


Instance Scheduler on AWS automates the starting and stopping of various AWS services, including Amazon Elastic Compute Cloud (Amazon EC2), Amazon EC2 Auto Scaling Groups, and Amazon Relational Database Service (Amazon RDS) instances. Automating this process helps reduce operational costs by stopping and starting resources as needed. This AWS Solution uses resource tags and AWS Lambda to automatically stop and start instances based on a schedule you define, and it can be deployed across multiple AWS Regions.

When compared to a setup where you leave all your instances continuously running at full utilization (even when those resources aren't being used), this solution can lead to significant cost savings by aligning your workloads with the AWS Well-Architected Cost Optimization best practices.


## <a name="design">📐 Architecture Diagram</a>

![Blank diagram-16](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/157d5e88-880e-472d-9adc-8a8db298049f)



## Scenario

A company has a development ec2 web server that is only used during office hours which is 9:00 a.m. to 5:00 p.m. Monday to Friday the company is looking for ways to reduce their Cloud cost and they've realized that by turning off the ac2 instance when it's not in use they'll be able to save about 60% of cost they want you to figure out an automated way to stop the instances at the end of the workday at 5:00 p.m. and start it at the beginning of the workday at 9: a.m.


## <a name="steps">☑️ Steps</a>

The procedure for deploying this architecture on AWS consists of the following steps:

Step 1: Creating the Instance 

Step 2: Creating the AMI Policy

Step 3: Creating the Lambda functions

Step 4: Creating the Schedules Using Cloud Watch


## ➡️ Step 1 - Creating the Instance 

