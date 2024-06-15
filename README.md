# Setup an SFTP Server on AWS EC2 using Docker

## Step 1: Create an AWS EC2 Instance

1. **Go to AWS Management Console**: [AWS Management Console](https://aws.amazon.com/).
2. **Launch a New EC2 Instance**:
   - Navigate to `EC2` > `Instances`.
   - Click `Launch Instances`.
   - Choose an appropriate Amazon Machine Image (AMI) (e.g., `Ubuntu Server 20.04 LTS`).
   - Select an instance type (e.g., `t2.micro` for free tier or `t3.medium` for better performance).
   - Configure instance details as needed.
   - Add storage (increase the default storage size if necessary, e.g. 200GiB).
   - Add tags (optional).
   - Configure security group:
     - Allow `SSH` (port 22) from your IP.
     - Allow custom TCP rule (port 2222) from any IP.
   - Review and launch the instance.

3. **Download the Key Pair**:
   - Create a new key pair or use an existing one.
   - Download the key pair (`.pem` file).

## Step 2: Connect to Your EC2 Instance

1. **Open a terminal** (or use PuTTY on Windows).
2. **Set the permissions for the key pair file**:
   ```bash
   chmod 400 /path/to/your-key-pair.pem
   ```

3. **Connect to the instance**:
   ```bash
   ssh -i /path/to/your-key-pair.pem ubuntu@your-ec2-public-dns
   ```

## Step 3: Install Docker and Docker Compose on Your EC2 Instance

1. **Update the package list**:
   ```bash
   sudo apt-get update
   ```

2. **Install prerequisites**:
   ```bash
   sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
   ```

3. **Add Docker’s official GPG key**:
   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```

4. **Add Docker repository**:
   ```bash
   sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```

5. **Update the package list again**:
   ```bash
   sudo apt-get update
   ```

6. **Install Docker**:
   ```bash
   sudo apt-get install docker-ce
   ```

7. **Start Docker**:
   ```bash
   sudo systemctl start docker
   ```

8. **Enable Docker to start on boot**:
   ```bash
   sudo systemctl enable docker
   ```

9. **Install Docker Compose**:
   ```bash
   sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   ```

## Step 4: Run an SFTP Server using Docker Compose

1. **Create a directory for your Docker Compose configuration**:
   ```bash
   mkdir sftp-server
   cd sftp-server
   ```

2. **Create a `docker-compose.yml` file**:
   ```yaml
   version: '3.1'

   services:
     sftp:
       image: atmoz/sftp
       container_name: sftp_server
       ports:
         - "2222:22"
       volumes:
         - /path/to/sftpdata:/home/user/upload
       command: user:pass:1001
   ```

   - Replace `user` and `pass` with your desired SFTP username and password.
   - Replace `/path/to/sftpdata` with the path where you want to store the SFTP data.

3. **Set permissions for the SFTP data directory**:
   ```bash
   sudo chown -R 1001:1001 /path/to/sftpdata
   ```

4. **Start the Docker Compose application**:
   ```bash
   sudo docker-compose up -d
   ```

## Step 5: Open Firewall Ports on AWS

1. **Ensure port 2222 is open** in the security group associated with your EC2 instance:
   - Navigate to `EC2` > `Security Groups`.
   - Select the security group associated with your instance.
   - Edit the inbound rules to ensure port 2222 is open for TCP traffic from any IP.

## Step 6: Connect to the SFTP Server

1. Use an SFTP client (e.g., FileZilla) to connect to your SFTP server.
   - **Host**: `YOUR_EC2_PUBLIC_IP`
   - **Port**: `2222`
   - **Protocol**: `SFTP`
   - **Logon Type**: `Normal`
   - **User**: `user`
   - **Password**: `pass`

## Step 7: SSH into the SFTP Server Container

1. **Navigate to your Docker Compose directory**:
   ```bash
   cd /path/to/sftp-server
   ```

2. **SSH into the `sftp` service container**:
   ```bash
   sudo docker-compose exec sftp bash
   ```

3. **Check logs from the `sftp` service container**:
   ```bash
   sudo docker-compose logs sftp
   ```

Now you should have a functional SFTP server running on an AWS EC2 instance using Docker Compose, ready to receive large files. You can also SSH into the container if needed.
