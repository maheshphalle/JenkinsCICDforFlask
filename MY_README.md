# 🏆 Flask CI/CD Pipeline with Cloud-Based Jenkins & GitHub Actions

This project sets up a **CI/CD pipeline** for a **Flask web application** using **Jenkins (cloud-based)** and **GitHub Actions**, enabling automated **testing and deployment** on an **AWS EC2 instance**.

---

## **📌 Project Overview**
- **Jenkins URL:** [https://jenkinsacademics.herovired.com/](https://jenkinsacademics.herovired.com/)
- **Technologies Used:**
  - **Flask** (Python Web Framework)
  - **GitHub Actions** (CI/CD Workflow)
  - **Cloud-Based Jenkins** (CI/CD Automation)
  - **AWS EC2** (Cloud Deployment)
  - **PyTest** (Testing Framework)
- **Goal:** Automate build, test, and deployment of the Flask app.

---

## **🚀 Jenkins CI/CD Pipeline Setup (Cloud-Based Jenkins)**
### **1️⃣ Fork & Clone Repository**
1. Fork the repository from **GitHub**.
2. Clone it to your EC2 instance:
   ```bash
   git clone https://github.com/maheshphalle/JenkinsCICDforFlask.git
   cd JenkinsCICDforFlask
   ```

### **2️⃣ Configure Cloud-Based Jenkins Job**
1. **Login to Jenkins:** [https://jenkinsacademics.herovired.com/](https://jenkinsacademics.herovired.com/)
2. **Create a New Pipeline Job:**
   - Click on **New Item** > **Pipeline**
   - Enter the job name **maheshFlask-CI-CD**
   - Select **Pipeline** and click **OK**
3. **Set Up SCM (Source Code Management):**
   - Under **Pipeline Definition**, select **Pipeline script from SCM**
   - **SCM:** Git
   - **Repository URL:** `https://github.com/maheshphalle/JenkinsCICDforFlask.git`
   - **Branch:** `main`
   - **Script Path:** `Jenkinsfile`

### **3️⃣ Add SSH Key for GitHub Access**
Since Jenkins needs access to your GitHub repository, you need to add an SSH key:
```bash
ssh-keygen -t rsa -b 4096 -C "your-email@example.com"
cat ~/.ssh/id_rsa.pub  # Copy this key
```
- **Go to GitHub → Settings → SSH Keys → Add New SSH Key**
- Paste the **public key** and save.

### **4️⃣ Create & Push Jenkinsfile**
Inside your repository, create `Jenkinsfile`:
```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'python3 -m pip install --user -r requirements.txt'
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
                pkill -f app.py || true
                nohup python3 app.py > app.log 2>&1 &
                '''
            }
        }
    }
    
    post {
        success {
            emailext subject: "✅ SUCCESS: Flask CI/CD Pipeline",
                     body: "Deployment successful!",
                     to: "mahesh.phalle@Outlook.com"
        }
        failure {
            emailext subject: "❌ FAILURE: Flask CI/CD Pipeline",
                     body: "Build failed. Check logs.",
                     to: "mahesh.phalle@Outlook.com"
        }
    }
}
```
```bash
git add Jenkinsfile
git commit -m "Added Jenkins pipeline"
git push origin main
```

### **5️⃣ Run & Debug Jenkins Job**
1. **Go to Jenkins Dashboard → Click "Build Now"**
2. If errors occur:
   - Check **Console Output** for missing dependencies.
   - If Python dependencies fail, run:
     ```bash
     python3 -m ensurepip --default-pip
     python3 -m pip install --user --upgrade pip
     ```
   - If `ModuleNotFoundError: No module named 'flask'`, run:
     ```bash
     export PYTHONPATH=$PWD
     python3 app.py
     ```
3. **Verify app is running:**
   ```bash
   curl http://localhost:5000
   ```

---

## **🔥 GitHub Actions CI/CD Workflow**
### **1️⃣ Create & Push Workflow File**
Inside `.github/workflows/ci-cd.yml`:
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
```bash
git add .github/workflows/ci-cd.yml
git commit -m "Added GitHub Actions workflow"
git push origin main
```
