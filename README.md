
# Setup an SFTP Server on GCP VM using Docker

## Step 1: Create a GCP VM Instance

1. **Go to Google Cloud Console**: [Google Cloud Console](https://console.cloud.google.com/).
2. **Create a New VM Instance**:
   - Navigate to `Compute Engine` > `VM instances`.
   - Click `Create Instance`.
   - Choose an appropriate name for your instance.
   - Select the `Region` and `Zone`.
   - Choose the `Machine type` (e.g., `n1-standard-1`).
   - Under `Boot disk`:
     - Click `Change` to select `Ubuntu` and choose an appropriate version (e.g., `Ubuntu 20.04 LTS`).
     - Adjust disk size if necessary, e.g. 200GiB.
   - Click `Select` and then `Create`.

## Step 2: Connect to Your VM Instance

1. Once the instance is created, click on the `SSH` button next to your instance to open an SSH terminal.

## Step 3: Install Docker and Docker Compose on Your VM

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

3. **Set permissions for the SFTP data directory:**:
   ```bash
   sudo chown -R 1001:1001 /path/to/sftpdata
   ```
   - Replace `/path/to/sftpdata` with the path where you want to store the SFTP data.

4. **Start the Docker Compose application**:
   ```bash
   sudo docker-compose up -d
   ```

## Step 5: Open Firewall Ports on GCP

1. **Open port 2222**:
   - Go to `VPC network` > `Firewall rules`.
   - Click `Create firewall rule`.
   - Set the `Name`, e.g., `sftp-port-2222`.
   - Set `Targets` to `All instances in the network`.
   - Set `Source IP ranges` to `0.0.0.0/0`.
   - Set `Allowed protocols and ports` to `tcp:2222`.
   - Click `Create`.

## Step 6: Connect to the SFTP Server

1. Use an SFTP client (e.g., FileZilla) to connect to your SFTP server.
   - **Host**: `YOUR_VM_EXTERNAL_IP`
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


Now you should have a functional SFTP server running on a GCP VM using Docker Compose, ready to receive files. You can also SSH into the container if needed.
