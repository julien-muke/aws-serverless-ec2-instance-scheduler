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


6. Enter a Policy name `start-ec2-instance`
7. Under description add a short explanation for this policy.

![5](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/d78b1a3e-443a-4b50-846c-193decfab67b)

Note: We have created a policy for starting instances. We also need to create a policy for stopping the instances. 
This is because we are going to create two Lambda functions, one for starting and one for stopping the instances. Each function will have its own role, and we will attach these two policies to their respective roles.

8. We are going to repeat the same step 2 for Creating Stopping Policy, except:
<br>* In Actions allowed, choose the actions to add to the policy, we are going to search and choose `DescribeInstances` and `StopInstances` then choose Next.
<br>* Enter a Policy name `stop-ec2-instance`


## ➡️ Step 3 - Creating the Lambda functions

To create a Hello world Lambda function with the console:

1. Open the [Functions page](https://console.aws.amazon.com/lambda/home#/functions) of the Lambda console.
2. Choose Create function.


![Screenshot 2024-07-05 at 16 42 02](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/209556d2-4095-4af1-94c4-10a67773699d)


3. Select Author from scratch.
4. In the Basic information pane, for Function name enter `start-ec2-demo`
5. For Runtime, choose either Python 3.
6. Leave architecture set to x86_64 and choose Create function.

![8](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/df697866-4575-48eb-8156-1fb7d059b8a8)


You now use the console's built-in code editor to replace the Hello world code that Lambda created with your own function code.

Copy the following python code and paste it into the console's built-in code editor.

Note: Replace the `region_name` to your corresponding region, and replace the `instance_id` to corresponding instance ID which you can find in the EC2 instance console where we've created one.

```python
import boto3

def lambda_handler(event, context):
    # Initialize the EC2 client
    ec2_client = boto3.client('ec2', region_name='us-east-1') # Replace your Region name

    # Specify the instance ID of the EC2 instance you want to start
    instance_id = 'i-051262059d6f6350a' # Replace your Own Instance ID

    # Start the EC2 instance
    try:
        response = ec2_client.start_instances(InstanceIds=[instance_id], DryRun=False)
        print(f"Starting EC2 instance {instance_id}...")
        print(f"Response: {response}")
        return {
            'statusCode': 200,
            'body': f"EC2 instance {instance_id} is being started."
        }
    except Exception as e:
        print(f"Error starting EC2 instance: {e}")
        return {
            'statusCode': 500,
            'body': f"Error starting EC2 instance: {str(e)}"
        }

```


![9](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/db4354b0-ebda-4b61-b711-8b2204caed6b)





