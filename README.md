https://roadmap.sh/projects/ssh-remote-server-setup
# AWS EC2 Setup with SSH Key Configuration

## Project Overview
In this project, I set up a remote Linux server on **AWS EC2**, configured it to allow SSH access with two SSH key pairs, and secured it with **fail2ban** to prevent brute-force login attempts.

## Steps Taken

### 1. **Create and Configure an AWS EC2 Instance**

- **Launch EC2 Instance**:
  - I created a new EC2 instance (Ubuntu 20.04 or Amazon Linux 2) through the AWS Management Console.
  - I selected the `t2.micro` instance type, which is eligible for the free tier.
  - I configured the instance’s **security group** to allow incoming SSH traffic on port 22 from my IP address.

- **Create SSH Key Pair**:
  - During the EC2 instance setup, I created an **SSH key pair** to securely access the instance.
  - I downloaded the private key file (`.pem`) and stored it safely.

- **Public IP Address**:
  - Once the EC2 instance was running, I obtained the **public IP address** of the instance to use for SSH connections.

### 2. **Generate Two New SSH Key Pairs Locally**

- **Create SSH Keys**:
  - On my local machine, I generated two SSH key pairs using the `ssh-keygen` command:
  
    ```bash
    ssh-keygen -t rsa -b 2048 -f ~/.ssh/key1
    ssh-keygen -t rsa -b 2048 -f ~/.ssh/key2
    ```

  - This generated the private keys `key1` and `key2` and their corresponding public keys `key1.pub` and `key2.pub`.

### 3. **Upload Public Keys to EC2 Instance**

- **Connect to EC2 Instance**:
  - I used the AWS-provided **EC2 private key** to SSH into the instance for the first time:
  
    ```bash
    ssh -i <path-to-aws-key.pem> ec2-user@<instance-public-ip>
    ```

- **Configure `authorized_keys`**:
  - I added the public keys (`key1.pub` and `key2.pub`) to the `~/.ssh/authorized_keys` file on the EC2 instance to allow SSH access using both keys.

    ```bash
    cat ~/key1.pub >> ~/.ssh/authorized_keys
    cat ~/key2.pub >> ~/.ssh/authorized_keys
    ```

  - I set the correct permissions for the `~/.ssh/authorized_keys` file:

    ```bash
    chmod 600 ~/.ssh/authorized_keys
    ```

### 4. **Test SSH Access Using Both Keys**

- **Test SSH with Key1**:
  - I connected to the EC2 instance using the first SSH key (`key1`) with the following command:
  
    ```bash
    ssh -i ~/.ssh/key1 ec2-user@<instance-public-ip>
    ```

- **Test SSH with Key2**:
  - Similarly, I connected using the second SSH key (`key2`):
  
    ```bash
    ssh -i ~/.ssh/key2 ec2-user@<instance-public-ip>
    ```

### 5. **Configure SSH Aliases in `~/.ssh/config`**

To simplify the SSH connection process, I configured SSH aliases:

1. **Edit `~/.ssh/config`**:
   - I added the following entries to the `~/.ssh/config` file on my local machine:

   ```bash
   # Alias for key1
   Host myserver-key1
     HostName <instance-public-ip>
     User ec2-user
     IdentityFile ~/.ssh/key1
     IdentitiesOnly yes

   # Alias for key2
   Host myserver-key2
     HostName <instance-public-ip>
     User ec2-user
     IdentityFile ~/.ssh/key2
     IdentitiesOnly yes
