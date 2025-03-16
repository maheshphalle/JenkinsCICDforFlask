
```md
# 🏆 Flask CI/CD Pipeline with Jenkins & GitHub Actions

This project automates the **CI/CD pipeline** for a **Flask web application** using **Jenkins and GitHub Actions**.  
It enables automated **testing and deployment** to an **AWS EC2 instance**.

---

## **📌 Project Overview**
- **Original Repository:** [Forked from GitHub](https://github.com/maheshphalle/JenkinsCICDforFlask)
- **Technologies Used:**
  - **Flask** (Python Web Framework)
  - **GitHub Actions** (CI/CD Workflow)
  - **Jenkins** (CI/CD Automation)
  - **AWS EC2** (Cloud Deployment)
  - **PyTest** (Testing Framework)
- **Goal:** Automate the build, test, and deployment process.

---

## **🚀 Jenkins CI/CD Pipeline Setup**
### **1️⃣ Install Jenkins on AWS EC2**
Run the following commands on your EC2 instance:
```bash
sudo apt update && sudo apt install -y openjdk-11-jdk wget
wget -q -O - https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update && sudo apt install -y jenkins
```
Start and enable Jenkins:
```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
Get the initial password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
Go to **`http://<your-ec2-public-ip>:8080`** and complete the setup.

---

### **2️⃣ Configure Jenkins Job**
- **Go to Jenkins → New Item → Pipeline**
- **Under "Pipeline Definition"**, select **Pipeline script from SCM**.
- **Enter your GitHub Repository URL**.
- **Ensure your repository has a `Jenkinsfile`**.

---

### **3️⃣ Create `Jenkinsfile`**
📌 Inside your GitHub repository, create a `Jenkinsfile`:
```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '''
                echo "Installing dependencies..."
                python3 -m pip install --user -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh 'pytest'
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                echo "Stopping existing Flask app..."
                pkill -f app.py || true
                echo "Starting Flask app..."
                nohup python3 app.py > app.log 2>&1 &
                '''
            }
        }
    }
    
    post {
        success {
            emailext subject: "✅ SUCCESS: Flask CI/CD Pipeline",
                     body: "The Flask app was successfully deployed!",
                     to: "mahesh.phalle@Outlook.com"
        }
        failure {
            emailext subject: "❌ FAILURE: Flask CI/CD Pipeline",
                     body: "The Jenkins build has failed. Check logs.",
                     to: "mahesh.phalle@Outlook.com"
        }
    }
}
```
📌 **Commit and push it to GitHub.**

---

## **🔥 GitHub Actions CI/CD Workflow**
### **1️⃣ Create GitHub Actions Workflow File**
Create a new file in `.github/workflows/ci-cd.yml`:
```yaml
name: Flask CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  build-test-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: '3.12'

      - name: Install dependencies
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Run tests
        run: pytest

      - name: Deploy to EC2
        env:
          PRIVATE_KEY: ${{ secrets.EC2_SSH_PRIVATE_KEY }}
          HOST: ${{ secrets.EC2_HOST }}
          USER: ubuntu
        run: |
          echo "$PRIVATE_KEY" > private_key.pem
          chmod 600 private_key.pem
          ssh -o StrictHostKeyChecking=no -i private_key.pem $USER@$HOST <<EOF
            cd ~/JenkinsCICDforFlask
            git pull origin main
            pkill -f app.py || true
            nohup python3 app.py > app.log 2>&1 &
          EOF
```
📌 **Commit and push it to GitHub.**

---

## **🌐 Configure AWS EC2 for Public Access**
### **1️⃣ Update `app.py`**
Modify `app.py` to **listen on all IPs**:
```python
if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000, debug=True)
```

### **2️⃣ Allow External Access**
Update **AWS Security Group**:
- **Go to AWS EC2 → Security Groups → Inbound Rules**.
- **Add a rule:**
  - **Type:** Custom TCP
  - **Port:** `5000`
  - **Source:** `0.0.0.0/0`

✅ **Now, access the app via:**  
```
http://<YOUR-EC2-PUBLIC-IP>:5000
```

---

git push origin main
```

✅ **Now your `MY_README.md` contains the full step-by-step process without modifying the original README.md.**  

📌 **Let me know if you need any changes! 🚀**
