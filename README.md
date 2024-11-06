## **Automating EC2 Instance Creation with Terraform** 

Hey, devs! Don’t worry you're at the right place! 😉

If you’ve ever had to create multiple EC2 instances through the AWS dashboard, you know it can be a time-consuming process. But there’s a smarter way: Infrastructure as Code (IaC) using Terraform! With Terraform, we can automate the creation and management of AWS resources, including EC2 instances, making our infrastructure repeatable and manageable with just a few commands. Let me walk you through the setup!

## 🛠 Prerequisites
Before diving into Terraform, make sure you have:

1.**AWS CLI Configured**: The AWS CLI should be set up and configured on your machine with the necessary permissions.

```
aws configure #configuring aws account 
```
Enter your access keys and secret access keys when prompted, and you’re good to go!

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/dgakb46vusisikp6789s.png)

2.**Terraform Installed**: Ensure Terraform is installed. 

[Terraform Documentation] (https://developer.hashicorp.com/terraform/install)

Once these are ready, you’re all set for automation! 

## Setting Up Your Terraform Project:

First, create a new directory for your Terraform project, which will house all the necessary configuration files.

```

mkdir ec2_gen #making a directory
cd ec2_gen    

```

Inside this directory, we’ll create a Terraform configuration file that defines our EC2 instances.

## 📝 Writing the Terraform Configuration

Use a text editor to create the main.tf file. Here, we’ll define our AWS provider, configure the region, and specify the resource (EC2 instances) we want to create.

```
vim main.tf

```

Now, add the following configuration to main.tf:

```
# Specifies Terraform block with required providers and versions
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"      # Specifies the source for the AWS provider
      version = "~> 4.16"            # Version constraint for the AWS provider
    }
  }
  required_version = ">= 1.9.8"      # Minimum Terraform version required
}

# Configures the AWS provider with a specific region
provider "aws" {
  region = "ap-south-1"              # AWS region (e.g., ap-south-1 for Asia Pacific, Mumbai)
}

# Defines an EC2 instance resource
resource "aws_instance" "my_ec2_instance" {
  ami           = "ami-04a37924ffe27da53"  # Amazon Linux 2 AMI ID (adjust to your region)
  instance_type = "t2.micro"               # Instance type (e.g., t2.micro)
  count         = 2                        # Number of instances to create (2 in this case)

  # Tags help to identify and manage resources
  tags = {
    Name = "terraform-instance-${count.index + 1}"  # Tag instances with unique names
  }
}

# Outputs the public IPs of the created EC2 instances
output "ec2_public_ips" {
  value = aws_instance.my_ec2_instance[*].public_ip  # Outputs the public IP addresses as a list
}

```

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/dvjbq8sm85cieuhy3fnn.png)


## 🔍 Explanation of the Code
Provider Block: This block specifies the AWS provider details, including the region. Make sure to adjust the region if you’re working outside of ap-south-1.

Resource Block: Here, we define the aws_instance resource. The count parameter lets us create multiple instances—in this case, 2 EC2 instances.

Tags: We use tags to help identify instances. The name will appear as "example-instance-1," "example-instance-2," and so on.

Output Block: This block outputs the public IPs of our newly created EC2 instances, which can be useful for further automation or SSH access.

##  Running the Terraform Code
With the configuration complete, let’s initialize and apply it. Here’s the sequence of commands you need to run:

Initialize Terraform: Sets up the working directory and downloads the necessary provider plugins.

```
terraform init
```

Validate the Configuration: Checks for syntax errors or misconfigurations.

```
terraform validate
```
![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ak1vachmazx7hhfr7l84.png)

Plan the Deployment: Shows what Terraform will do without making any actual changes. This is a great way to confirm before deploying.

```
terraform plan
```

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/nxa2n6nie7odqoulhsrm.png)


![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/yart8ly5v319h9d84z6f.png)



Apply the Changes: Creates the resources defined in main.tf.
```
terraform apply
```

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a2a048kpync28qdw2lqw.png)


During the apply step, Terraform will prompt you to confirm. Type yes to proceed.

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/10tiyqaxlx8z56efmquz.png)


![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/o3n08xaotw12vi24i71t.png)



🎉 Verify the Results
Head over to the AWS dashboard and check your EC2 instances. You should see the instances created with the specified tags and configurations. 🚀

![Image description](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/a50mk8s8apxezmjaokpn.png)

## wait wait wait...
Created hundreds instances just for fun and don't know how to terminate them? 
Don't just do it manually, haha!!

Don’t worry! Just use:

```
terraform destroy
```

This command will remove all resources defined in your configuration. Thank me later! 😉


**Bonus tip**
You can choose Amazon Machine Image by changing AMI ID in the code.
