**How to add an AWS account in Dynatrace for monitoring purposes, using the key-based access method.**

_Step 1: Create a User in AWS for Dynatrace Access_
```- Go to AWS Management Console.
- Open IAM service.
- Create a policy for Dynatrace:
- Click on 'Policies' > 'Create Policy'.
- Choose the 'JSON' tab.
- Use the predefined policy from Dynatrace documentation (link in video description).
- Paste the policy JSON.
- Click 'Next' > Add a tag: Key - Dynatrace, Value - Monitoring Policy.
- Click 'Review' > Name the policy (e.g., dynatrace_monitoring_policy) > Create policy
```

![Screenshot-1](https://github.com/user-attachments/assets/a47222d2-9412-4a05-b77e-93f28177f39c)

Step 2: Create a User and Attach Policy
```- Click on 'Users' > 'Add users'.
- Username: dynatrace_monitoring_user (no spaces allowed).
- Select 'Programmatic access'.
- Permissions: Attach existing policies > Select 'dynatrace_monitoring_policy' created earlier.
- Add tags if needed.
- Review and create user.
- Download the .csv file containing the Access Key ID and Secret Access Key
```

![Screenshot-2](https://github.com/user-attachments/assets/91dc923f-eaa0-4dcd-81df-b598a5263ca6)
![Screenshot-3](https://github.com/user-attachments/assets/cd7e35f3-7661-48ea-a834-9bd02026d19d)

Step 3: Add AWS Account in Dynatrace
```- Open Dynatrace console.
- Go to 'Settings' > 'Cloud and Virtualization' > Select 'AWS'.
- Click on 'Connect New Instance'.
- Be aware that you will be charged based on CloudWatch metrics usage.
- Enter a connection name (e.g., dynatrace_aws_cloud_integration).
- Select 'Key-based authentication'.
- Enter Access Key and Secret Access Key from the created user.
- AWS Partition: Default.
- Resources to monitor: All.
- Click on 'Connect'
```

![Screenshot-4](https://github.com/user-attachments/assets/c37937c0-fa18-43f7-8b18-d1a53f3e5723)
![Screenshot-5](https://github.com/user-attachments/assets/9d91a6f8-e59e-4e02-86d3-a2a308a8348e)

Dynatrace will verify and start monitoring your AWS account. Metrics from CloudWatch will be fetched and displayed in Dynatrace monitoring consoles.

**Note**: You can also add multiple AWS accounts using the same method or via role-based authentication.





