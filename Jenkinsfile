pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '''
                echo "Checking Python Version..."
                python3 --version || echo "Python is not installed!"
                which python3 || echo "Python binary not found!"
                
                echo "Checking pip..."
                python3 -m pip --version || echo "pip is not installed!"

                echo "Installing Python & pip if missing..."
                sudo apt update
                sudo apt install -y python3 python3-pip
                
                echo "Installing dependencies..."
                pip install --upgrade pip
                pip install -r requirements.txt
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
                sh 'python3 app.py &'
            }
        }
    }
}
