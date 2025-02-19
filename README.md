

---

# ACIT 4640 - Lab Week 7: Ansible Configuration for EC2 Instances

This repository contains the files necessary to configure EC2 instances using Ansible. The playbook installs Nginx, sets up directories, and configures the Nginx server for a simple static site.

## Prerequisites

- **Terraform** must be set up to create EC2 instances.
- **Ansible** must be installed on your local machine.
- **AWS EC2 instances** must be running.
- An **SSH key** (`aws`) is required to authenticate with EC2 instances.

## Setup Steps

### 1. Generate a New SSH Key

Run the following command to generate a new SSH key pair and add it to the `~/.ssh/` directory.

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/aws -N ""
```

- `-t rsa`: Specifies the key type (RSA).
- `-b 4096`: Creates a 4096-bit key for improved security.
- `-f ~/.ssh/aws`: Saves the key to the `~/.ssh/aws` directory with the name `aws`.
- `-N ""`: Creates the key without a passphrase.

### 2. Import the Public Key to AWS

Use the provided `import_lab_key` script to upload the public key to AWS. This will allow Ansible to authenticate using the `aws` SSH key.

```bash
./import_lab_key ~/.ssh/aws.pub
```

### 3. Run Terraform to Create EC2 Instances

In the `terraform` directory, initialize and apply the configuration to create EC2 instances:

```bash
cd terraform
terraform init
terraform apply
```

Terraform will output the public IPs and DNS names for your EC2 instances.

### 4. Update the Ansible Inventory File

Edit the `ansible/inventory/hosts.yml` file to include the public IPs or DNS names of your EC2 instances. The file should look something like this:

```yaml
all:
  children:
    web:
      hosts:
        server-one:
          ansible_host: 35.91.73.66
          ansible_user: ubuntu
          ansible_ssh_private_key_file: ~/.ssh/aws
        server-two:
          ansible_host: 35.94.123.46
          ansible_user: ubuntu
          ansible_ssh_private_key_file: ~/.ssh/aws
```

Replace `35.91.73.66` and `35.94.123.46` with your actual IP addresses or DNS names.

### 5. Run the Ansible Playbook

Run the playbook to configure your EC2 instances:

```bash
ansible-playbook -i ansible/inventory/hosts.yml ansible/playbook.yml
```

This command will:
- Install Nginx on both EC2 instances.
- Create necessary directories.
- Copy configuration files.
- Create a symbolic link for Nginx configuration.
- Generate an `index.html` file from a template.
- Reload and enable the Nginx service.

### 6. Verify Nginx Installation

After the playbook runs, open a web browser and navigate to the public IP or DNS of either EC2 instance to verify that Nginx is running:

```
http://<Public_IP_or_DNS_of_EC2_Instance_1>
```

You should see the static website served by Nginx.

---

## Cleanup

When you're finished, run Terraform destroy to remove the EC2 instances:

```bash
cd terraform
terraform destroy
```

Additionally, you can delete the SSH key from AWS using the `delete_lab_key` script:

```bash
./delete_lab_key
```

---
