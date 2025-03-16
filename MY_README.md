# 📝 My Notes for Flask CI/CD Project

This document contains my personal notes on setting up and running the **Flask CI/CD pipeline using Jenkins and GitHub Actions**.

## ✅ **Project Overview**
- **Original Repository:** [Link to Forked Repo](https://github.com/maheshphalle/JenkinsCICDforFlask)
- **Technologies Used:** Python, Flask, Jenkins, GitHub Actions, AWS EC2
- **Goal:** Automate testing & deployment of Flask app.

## 🔹 **Jenkins Setup**
1. Installed Jenkins on AWS EC2.
2. Configured Jenkins job with `Jenkinsfile` containing:
   - **Build Stage:** Install dependencies.
   - **Test Stage:** Run PyTest.
   - **Deploy Stage:** Restart Flask app on EC2.

## 🔹 **GitHub Actions Setup**
1. Added **GitHub Actions workflow** (`ci-cd.yml`) in `.github/workflows/`
2. Secrets added to GitHub:
   - `EC2_SSH_PRIVATE_KEY`
   - `EC2_HOST`
3. Automatically deploys Flask app after push.

## 📸 **Screenshots**
- Jenkins pipeline run ✅
- GitHub Actions workflow ✅

## 🔗 **Final Submission**
- GitHub Repository: [https://github.com/maheshphalle/JenkinsCICDforFlask](https://github.com/maheshphalle/JenkinsCICDforFlask)
- Submission via **VLearn**.

🚀 **Project successfully deployed! 🎉**
