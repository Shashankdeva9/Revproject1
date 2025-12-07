# Jenkins Configuration Guide

## 1. Install Jenkins

### Windows:
```bash
# Download and run Jenkins installer
# https://www.jenkins.io/download/
# Start Jenkins service
```

### Linux:
```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
sudo systemctl start jenkins
```

Access: `http://localhost:8080`

## 2. Initial Setup

1. Get initial admin password:
```bash
# Windows: C:\Program Files\Jenkins\secrets\initialAdminPassword
# Linux: /var/lib/jenkins/secrets/initialAdminPassword
```

2. Install suggested plugins
3. Create admin user

## 3. Install Required Plugins

Dashboard → Manage Jenkins → Plugins → Available Plugins

Install:
- ✅ **Docker Pipeline**
- ✅ **Maven Integration**
- ✅ **Git**
- ✅ **Pipeline**

Click "Install" and restart Jenkins.

## 4. Configure Tools

### Maven Configuration
1. Manage Jenkins → Tools
2. Scroll to **Maven installations**
3. Click "Add Maven"
   - Name: `Maven3`
   - ✅ Install automatically
   - Version: `3.9.5`
4. Save

### JDK Configuration
1. Same page, scroll to **JDK installations**
2. Click "Add JDK"
   - Name: `JDK17`
   - ✅ Install automatically
   - Version: `jdk-17.0.9+9`
3. Save

### Docker (if not installed)
```bash
# Install Docker on Jenkins server
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

## 5. Add Docker Hub Credentials

1. Manage Jenkins → Credentials
2. Click **(global)** domain
3. Click "Add Credentials"
   - Kind: `Username with password`
   - Username: `your-dockerhub-username`
   - Password: `your-dockerhub-password`
   - ID: `docker-hub`
4. Click "Create"

## 6. Create Pipeline Job

1. Dashboard → New Item
2. Enter name: `RevTickets-Pipeline`
3. Select: **Pipeline**
4. Click OK

### Configure Pipeline:

**General:**
- ✅ GitHub project: `https://github.com/your-username/revticketsnew`

**Build Triggers:**
- ✅ Poll SCM: `H/5 * * * *` (checks every 5 min)
- OR ✅ GitHub hook trigger (for push events)

**Pipeline:**
- Definition: `Pipeline script from SCM`
- SCM: `Git`
- Repository URL: `https://github.com/your-username/revticketsnew.git`
- Branch: `*/main`
- Script Path: `Jenkinsfile`

Click **Save**

## 7. Test Build

1. Click "Build Now"
2. Check Console Output
3. Verify Docker image created:
```bash
docker images | grep revtickets
```

## 8. GitHub Webhook (Optional - Auto Trigger)

### In GitHub:
1. Repository → Settings → Webhooks
2. Add webhook:
   - Payload URL: `http://your-jenkins-url:8080/github-webhook/`
   - Content type: `application/json`
   - Events: `Just the push event`
3. Save

### In Jenkins:
- Job → Configure → Build Triggers
- ✅ GitHub hook trigger for GITScm polling

## Troubleshooting

### Maven not found:
```bash
# Verify Maven installed
mvn -version
```

### Docker permission denied:
```bash
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

### Git authentication:
Add GitHub credentials in Jenkins:
- Manage Jenkins → Credentials → Add
- Kind: Username with password
- Use in Pipeline SCM configuration

## Verify Setup

Run this in Jenkins Script Console (Manage Jenkins → Script Console):
```groovy
println "Jenkins Home: ${System.getenv('JENKINS_HOME')}"
println "Java: ${System.getProperty('java.version')}"
```

✅ Setup Complete! Push code to trigger automatic builds.
