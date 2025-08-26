## Create a simple Node.js app, dockerize it, and automate build, test, push, and deployment using Jenkins.

## Requirements
- Ubuntu 24.04 or similar Linux environment
- Jenkins installed and running
- Docker installed
- Git installed
- Docker Hub account
- AWS account for EC2 deployment

## Step 1: Install Docker
```bash
echo "Installing prerequisites..."
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg lsb-release

echo "Setting up Docker GPG key and using 'jammy' repo for Ubuntu 24.04..."
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
  sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

ARCH=$(dpkg --print-architecture)
UBUNTU_CODENAME="jammy"  # Force Jammy repo for Docker

echo "Adding Docker APT repository from jammy..."
echo "deb [arch=${ARCH} signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu ${UBUNTU_CODENAME} stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

echo "Installing Docker..."
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

echo "Adding current user to Docker group..."
sudo usermod -aG docker $USER
newgrp docker

echo "Verifying Docker installation..."

docker run hello-world
```

## Step 2: Install Jenkins
```bash
echo "Installing Java (required for Jenkins)..."
sudo apt-get update
sudo apt-get install -y openjdk-17-jdk

echo "Adding Jenkins repo and key..."
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null

echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/" | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

echo "Installing Jenkins..."
sudo apt-get update
sudo apt-get install -y jenkins

echo "Starting Jenkins service..."
sudo systemctl enable jenkins
sudo systemctl start jenkins

echo "Getting initial admin password..."
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Notes:
- Jenkins runs on port 8080 by default → http://server-ip:8080
- Copy the admin password shown in last step to unlock Jenkins.
- Install suggested plugins during first login.
- Also, Install plugins like dockerplugin, sshagent

## Step 3: Prepare Jenkins for Docker
```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
sudo -u jenkins docker --version
sudo -u jenkins docker ps
```

## Step 4: Configure Credentials
In Jenkins Dashboard → Manage Jenkins → Credentials → Add:
- DockerHub Credentials (username/password or token)
- EC2 SSH Key (for deployment)

## Step 5: Configure Credentials
In Jenkins Dashboard → Manage Jenkins → Credentials → Add:
- DockerHub Credentials (username/password or token)
- EC2 SSH Key (for deployment)

## Step 6: Create GitHub Repo
Push your Node.js app + Dockerfile + Jenkinsfile.

## Step 7 – Setup Webhook (GitHub → Jenkins)
- Go to GitHub repo → Settings → Webhooks → Add Webhook
- Payload URL → http://<Jenkins-IP>:8080/github-webhook/
- Content type → application/json
- Trigger → Just the push event (This auto-triggers Jenkins when code is pushed.)

## Step 8 – Create Jenkins Pipeline
- In Jenkins → New Item → Pipeline → Configure → Select "Pipeline script from SCM" → Add GitHub repo link.
- Save & Build once to test.
- Under Build Triggers:
- Tick GitHub hook trigger for GITScm polling

## Step 9 – Verify
- Run docker ps on Target EC2 → should see new container.
- Visit http://<EC2-IP>:3001/health → should return success.
