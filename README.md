Setting up an FTP server on a GCP VM using Docker involves several steps. Below is a detailed tutorial on how to achieve this.

### Step 1: Create a GCP VM Instance

1. **Go to Google Cloud Console**: [Google Cloud Console](https://console.cloud.google.com/).
2. **Create a New VM Instance**:
   - Navigate to `Compute Engine` > `VM instances`.
   - Click `Create Instance`.
   - Choose an appropriate name for your instance.
   - Select the `Region` and `Zone`.
   - Choose the `Machine type` (e.g., `n1-standard-1`).
   - Under `Boot disk`, click `Change` to select `Ubuntu` and choose an appropriate version (e.g., `Ubuntu 20.04 LTS`).
   - Click `Select` and then `Create`.

### Step 2: Connect to Your VM Instance

1. Once the instance is created, click on the `SSH` button next to your instance to open an SSH terminal.

### Step 3: Install Docker on Your VM

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

### Step 4: Run an FTP Server in a Docker Container

1. **Pull an FTP server Docker image** (for this example, we will use `fauria/vsftpd`):
   ```bash
   sudo docker pull fauria/vsftpd
   ```

2. **Run the Docker container**:
   ```bash
   sudo docker run -d -p 21:21 -p 30000-30009:30000-30009 -e FTP_USER=user -e FTP_PASS=pass -e PASV_ADDRESS=YOUR_VM_EXTERNAL_IP -v /path/to/ftpdata:/home/vsftpd fauria/vsftpd
   ```

   - Replace `user` and `pass` with your desired FTP username and password.
   - Replace `YOUR_VM_EXTERNAL_IP` with the external IP address of your GCP VM.
   - Replace `/path/to/ftpdata` with the path where you want to store the FTP data.

### Step 5: Open Firewall Ports on GCP

1. **Open ports 21 and 30000-30009**:
   - Go to `VPC network` > `Firewall rules`.
   - Click `Create firewall rule`.
   - Set the `Name`, e.g., `ftp-ports`.
   - Set `Targets` to `All instances in the network`.
   - Set `Source IP ranges` to `0.0.0.0/0`.
   - Set `Allowed protocols and ports` to `tcp:21,tcp:30000-30009`.
   - Click `Create`.

### Step 6: Connect to the FTP Server

1. Use an FTP client (e.g., FileZilla) to connect to your FTP server.
   - Host: `YOUR_VM_EXTERNAL_IP`
   - Port: `21`
   - Protocol: `FTP`
   - Encryption: `Use explicit FTP over TLS if available`
   - Logon Type: `Normal`
   - User: `user`
   - Password: `pass`

Now you should have a functional FTP server running on a GCP VM using Docker, ready to receive large files.
