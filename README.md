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
5. For Runtime, choose either Python 3.12
6. Leave architecture set to x86_64 and choose Create function.

![8](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/df697866-4575-48eb-8156-1fb7d059b8a8)


You now use the console's built-in code editor to replace the Hello world code that Lambda created with your own function code.

Copy the following python code and paste in `lambda_function.py` file in the console's built-in code editor.

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


![9](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/75e1ccf2-23aa-43ed-9b21-e972a9fb5a31)


7. Select Deploy to update your function's code.
8. Select Test and Configure test event.
9. Enter Event name `start-instance-demo` then Save.

![10](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/0f3978fa-d03f-4d5b-835e-f0c8709c011c)

Note: Before we test the Lambda function, let's attach `start-ec2-Instance` policy to the Lambda function.

10. Choose Configuration Tab, then select Permissions, click `start-ec2-demo-role`

![12](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/13e4468f-87d6-49a4-8c2a-9db039d5aeb0)

11. Under Permissions policies, choose Attach policies.

![13](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/9527ec86-aa87-4d1f-93fc-6b6e528c3905)


12. Select `start-ec2-instance` policy then choose Add permissions


![14](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/dd071e75-3c83-4baf-b2ea-1a94832ca1f8)


13. You now test `start-ec2-demo` function and use the Lambda console and CloudWatch Logs to view records of your function’s invocation.

![Screenshot 2024-07-05 at 12 21 08](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/4d8f40ce-d69a-4dd1-8ea4-c61469486c18)


14. As you can see below the `start-ec2-demo` lambda function is working and the instance is running.


![Screenshot 2024-07-05 at 12 29 59](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/d29297f4-808b-48ad-9b4a-fede54dfc3be)


Note: Now you going to create a Lambda function for stopping the instance. You will reapet step 3, except:
<br>* For Function name enter `stop-ec2-demo` 
<br>* The only changes we have to make are to replace the default code with the stop EC2 instance code below, and attach the `stop-ec2-instance` policy we created for stopping instances to the role of this Lambda function.

```python
import boto3

def lambda_handler(event, context):
    # Initialize the EC2 client
    ec2_client = boto3.client('ec2', region_name='us-east-1') # Replace your Region name

    # Specify the instance ID of the EC2 instance you want to stop
    instance_id = 'i-051262059d6f6350a' # Replace your Own Instance ID

    # Stop the EC2 instance
    try:
        response = ec2_client.stop_instances(InstanceIds=[instance_id], DryRun=False)
        print(f"Stopping EC2 instance {instance_id}...")
        print(f"Response: {response}")
        return {
            'statusCode': 200,
            'body': f"EC2 instance {instance_id} is being stopped."
        }
    except Exception as e:
        print(f"Error stopping EC2 instance: {e}")
        return {
            'statusCode': 500,
            'body': f"Error stopping EC2 instance: {str(e)}"
        }

```

You now test `stop-ec2-demo`s function and use the Lambda console and CloudWatch Logs to view records of your function’s invocation.

![Screenshot 2024-07-05 at 12 27 04](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/92ceffba-47ac-4c5f-8c5d-a513e12aa089)


As you can see below the `stop-ec2-demo` lambda function is working and it's stopping the instance.

![Screenshot 2024-07-05 at 12 27 50](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/d320cc0c-e3e2-4b0c-9b4d-f37c70a59909)


## ➡️ Step 3 - Creating the Schedules Using Cloud Watch

To Creating a CloudWatch:

1. Open the CloudWatch console at https://console.aws.amazon.com/cloudwatch/
2. In the navigation pane on the left, choose Rules.
3. Under Rules, choose Create rule

![Screenshot 2024-07-05 at 12 32 17](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/844adfed-9e86-4f8d-8fc3-880df14d4a15)

4. Configure rule details, enter rule name `start-ec2-rule` , for Event bus choose Default, for Rule type choose `Schedule`, and then choose Continue in EventBridge Scheduler.

![Screenshot 2024-07-05 at 12 37 42](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/9ccc65f2-5ccc-4717-b795-c09feeab84eb)


5. Enter Schedule name `start-ecz-rule` and keep Schedule group as default.

![Amazon-EventBridge-Scheduler-us-east-1](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/136a9eaa-7649-47f6-933d-a87364b49d4d)


6. Under Schedule pattern, choose Recurring schedule as Occurrence.
7. Under Schedule type, choose Cron-based schedule.
8. Define the cron expression for the schedule, a date and time are displayed in your current time zone in UTC
format, e.g. "Wed, Nov 9, 2022 09:00 (UTC - 08:00)" for Pacific time.
9. Set off Flexible time window, keep Timeframe as default then click Next.

![Amazon-EventBridge-Scheduler-us-east-1 copy](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/5a51af3a-417d-44b9-a26a-c6d4c43e24ac)


10. Under Select target, choose Template targets as Target API, then select AWS Lambda Invoke

![Screenshot 2024-07-05 at 12 46 49](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/594f5f7f-4912-4c22-9c8c-6a4b0ab19d5e)


11. Under Invoke AWS Lambda, choose `start-ec2-demo` Lambda function, then click Next.

![Amazon-EventBridge-Scheduler-us-east-1(1)](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/24da5ab6-58d4-4a6b-8863-df540b4bd62d)


12. Under Settings, for an action after schedule completion, choose NONE.
13. Scroll down and keep everything else as default and choose Next.

![Amazon-EventBridge-Scheduler-us-east-1(2)](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/c997571f-e376-4481-93cd-2af796ed294d)


13. Review everything and click create schedule.

We have created a schedule for starting the instance every day at 8:00 AM.

Note: Next, we need to create a schedule for stopping instances. To create the schedule for stopping instances, follow the step 3, except:
<br>* Enter rule name as `stop-ec2-rule` 
<br>* We need to change the schedule time to 17:00 because it will stop the Lambda function at 17:00 IST (5:00 PM).
<br>* Under Invoke AWS Lambda, choose `stop-ec2-demo` Lambda function.

As you can see below, we have successfully created two schedules: one to start the instance every day at 8:00 AM and the other to stop the instance every day at 5:00 PM.

![Screenshot 2024-07-05 at 13 52 08](https://github.com/julien-muke/aws-serverless-ec2-instance-scheduler/assets/110755734/09c82cdb-a8e2-47bd-823e-9e1d5f5310d4)


## 💰 Cost

All services used are eligible for the AWS Free Tier. However, charges will incur at some point so it's recommended that you shut down resources after completing this tutorial.