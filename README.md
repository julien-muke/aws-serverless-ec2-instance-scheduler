# ![aws](https://github.com/julien-muke/Search-Engine-Website-using-AWS/assets/110755734/01cd6124-8014-4baa-a5fe-bd227844d263) AWS serverless system to automatically turn EC2 instances ON and OFF.


## <a name="introduction">🤖 Introduction</a>


Instance Scheduler on AWS automates the starting and stopping of various AWS services, including Amazon Elastic Compute Cloud (Amazon EC2), Amazon EC2 Auto Scaling Groups, and Amazon Relational Database Service (Amazon RDS) instances. Automating this process helps reduce operational costs by stopping and starting resources as needed. This AWS Solution uses resource tags and AWS Lambda to automatically stop and start instances based on a schedule you define, and it can be deployed across multiple AWS Regions.

When compared to a setup where you leave all your instances continuously running at full utilization (even when those resources aren't being used), this solution can lead to significant cost savings by aligning your workloads with the AWS Well-Architected Cost Optimization best practices.


## <a name="design">📐 Architecture Diagram</a>

![Blank diagram-16](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/4ed4e5fa-2ca2-4c95-8ad0-31a5821d9cd9)


## Scenario

A company has a development ec2 web server that is only used during office hours which is 9:00 a.m. to 5:00 p.m. Monday to Friday the company is looking for ways to reduce their Cloud cost and they've realized that by turning off the ac2 instance when it's not in use they'll be able to save about 60% of cost they want you to figure out an automated way to stop the instances at the end of the workday at 5:00 p.m. and start it at the beginning of the workday at 9: a.m.


## <a name="steps">☑️ Steps</a>

The procedure for deploying this architecture on AWS consists of the following steps:

Step 1: Launch an instance

Step 2: Creating the AMI Policy

Step 3: Creating the Lambda functions

Step 4: Creating the Schedules Using Cloud Watch


## ➡️ Step 1 - Launch an instance

To launch an instance:

1. Open the Amazon EC2 console at https://console.aws.amazon.com/ec2/
2. From the EC2 console dashboard, in the Launch instance pane, choose Launch instance.

![1](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/357298ad-320f-470b-abad-f37f2ee3a178)


3. Under Name and tags, for Name, enter a descriptive name for your instance `instance-scheduler`
4. Under Application and OS Images (Amazon Machine Image), select an AMI that is marked Free Tier eligible.
5. Under Instance type, for Instance type, choose `t2.micro`, which is eligible for the Free Tier.
6. Under Key pair (login), for Key pair name, for this demo let's choose Proceed without a key pair.
7. Keep everything else as default, and choose Launch instance.

![2](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/163ec200-8d15-423b-bcc4-9a0a010dc4a6)


## ➡️ Step 2 - Creating the AMI Policy

To use the visual editor to create a policy

1. open the IAM console at https://console.aws.amazon.com/iam/
2. In the navigation pane on the left, choose Policies. 
3. Choose Create policy.

![3](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/84f8c91e-01fa-43c2-8623-cdbbf63c1fe9)


4. In the Policy editor section, find the Select a service section, and then choose an AWS service, we are going to choose `EC2` as services.
5. In Actions allowed, choose the actions to add to the policy, we are going to search and choose `DescribeInstances` and `StartInstances` then choose Next.

![4](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/616a785d-dc2a-414a-a8d6-bc9854398737)


6. 


