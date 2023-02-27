# Week 0 — Billing and Architecture

## Homework

### Install AWS CLI

In order to use AWS CLI with Gitpod we'll do the below:

- Install the AWS CLI when our Gitpod environment launches
- Update our `.gitpod.yml` file to automate the AWS CLI installation
- Test the automation works as expected

Update the `.gitpod.yml` file to include the following task.

```yml
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT
```

With the above code, we'll ensure the AWS CLI installer is downloaded outside our repository, on `/workspace` so we don't add it to our GitHub repo.

### Create a new User and Generate AWS Credentials

In order to create a new IAM user, we can follow these [steps](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_create.html) from the AWS Documentation website and get familiar with the official AWS resources.

Once the IAM user is created, we can follow the below steps to generate the Access Keys:

- Open the `IAM console`
- In the navigation pane, choose `Users`
- Choose the name of the user whose access keys you want to create
- On the summary page of the user, choose the `Security Credentials` tab
- In the `Access Keys` section, choose `Create access key`. Select `Show` to view the new access key pair
- You can also `Download .csv file` and store the keys in a secure location
  
### Set Env Vars

We will set these credentials for the current bash terminal

```bash
export AWS_ACCESS_KEY_ID=""
export AWS_SECRET_ACCESS_KEY=""
export AWS_DEFAULT_REGION=us-east-1
```

We'll tell Gitpod to remember these credentials if we relaunch our workspaces

```bash
gp env AWS_ACCESS_KEY_ID=""
gp env AWS_SECRET_ACCESS_KEY=""
gp env AWS_DEFAULT_REGION=us-east-1
```

### Check that the AWS CLI is working and you are the expected user

Use the following commnad to get your account ID using AWS CLI.

```bash
aws sts get-caller-identity
```

It will show something liek this:

```bash
{
    "UserId": "AIDA6FVUREK4GJANRAH6S",
    "Account": "974262444728",
    "Arn": "arn:aws:iam::974262444728:user/iamadmin"
}
```

### Enable Billing

- In your Root Account go to the [Billing Page](https://console.aws.amazon.com/billing/)
- Under `Billing Preferences` Choose `Receive Billing Alerts`
- Save Prefrences

### Create SNS Topic

We have to create an SNS topic before we create an alarm. The SNS topic will send an alert when we get over the alarm threshold. More info [here](https://docs.aws.amazon.com/cli/latest/reference/sns/create-topic.html).

Run the following command to create an SNS Topic

```bash
aws sns create-topic --name billing-alarm
```

This will return a TopicArn. Make a note of this.

We'll create a subscription providing the TopicARN and our preferred Email address for notifications

```bash
aws sns subscribe \
    --topic-arn TopicARN \
    --protocol email \
    --notification-endpoint your@email.com
```

Check your email and confirm the subscription

![SNS Topic](assets/SNS%20Topic.jpg)

### Create Alarm

### Create and AWS Budget
