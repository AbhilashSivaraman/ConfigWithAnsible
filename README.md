# Ansible & CloudFormation Demonstration

## Prerequisites

Before proceeding, ensure you have:
- An AWS account with appropriate permissions.
- An EC2 instance with Amazon Linux 2.
- Access to an S3 bucket containing the `ec2-instance.yaml` CloudFormation template.

---

## Step 1: Install Required Packages and Tools

Run the following commands to set up the environment:

```bash
sudo yum update -y
sudo yum install -y python3
sudo yum install -y python3-pip
pip3 --version
sudo pip3 install ansible
sudo pip3 install botocore boto3
sudo yum install aws-cli -y
```

### Verify Installations
- Python3: `python3 --version`
- Pip: `pip3 --version`
- AWS CLI: `aws --version`
- Ansible: `ansible --version`

---

## Step 2: Set Up Directory Structure

Navigate to the home directory of the EC2 user and create the following structure:

```bash
cd /home/ec2-user
mkdir -p ansible/playbooks ansible/templates ansible/inventory
```

This structure organizes playbooks, templates, and inventory files for Ansible.

---

## Step 3: Create the Ansible Playbook

Navigate to the `playbooks` directory and create the `deploy_ec2_from_s3.yaml` file:

```bash
cd ansible/playbooks
touch deploy_ec2_from_s3.yaml
```

Add the following content to the file:

```yaml
---
- name: Deploy EC2 instance using CloudFormation from S3
  hosts: localhost
  gather_facts: no
  vars:
    ansible_python_interpreter: /usr/bin/python3
  tasks:
    - name: Create CloudFormation Stack
      amazon.aws.cloudformation:
        stack_name: MyNewEC2Stack
        state: present
        region: ap-south-1
        template_url: "https://cloudformbucket001.s3.ap-south-1.amazonaws.com/ec2-instance.yaml"
        capabilities:
          - CAPABILITY_IAM
```

Save the file.

---

## Step 4: Set Up IAM Role for Ansible

### Create an IAM Role
1. Log in to the AWS Management Console.
2. Navigate to **IAM Roles**.
3. Create a new role with the following permissions:
   - `AmazonEC2FullAccess`
   - `AWSCloudFormationFullAccess`
   - `AmazonS3ReadOnlyAccess`
4. Attach this role to the EC2 instance hosting Ansible.

---

## Step 5: Deploy the CloudFormation Stack

Navigate to the `ansible/playbooks` directory:

```bash
cd /home/ec2-user/ansible/playbooks
```

Run the following command to execute the Ansible playbook:

```bash
ansible-playbook deploy_ec2_from_s3.yaml
```

### Verify Deployment
- Check the AWS CloudFormation console to ensure the stack `MyNewEC2Stack` is created successfully.
- Verify the EC2 instance is launched.

---

## Troubleshooting

- **Missing Permissions**: Ensure the IAM role attached to your EC2 instance has the required permissions.
- **Template Not Found**: Verify the `template_url` points to a valid S3 location.
- **Ansible Module Errors**: Ensure all Python packages are up-to-date using `pip3 install --upgrade`.

---

## Conclusion

This setup demonstrates the power of automation and infrastructure as code, streamlining AWS resource management.



