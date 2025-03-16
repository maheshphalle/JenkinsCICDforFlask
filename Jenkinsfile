pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '''
                echo "Checking Python Version..."
                python3 --version

                echo "Checking pip..."
                python3 -m pip --version || echo "pip is not installed!"

                echo "Installing pip in user space..."
                python3 -m ensurepip --default-pip || true
                python3 -m pip install --user --upgrade pip

                echo "Installing dependencies..."
                python3 -m pip install --user -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh 'python3 -m pytest'
            }
        }

        stage('Deploy') {
            steps {
                sh 'python3 app.py &'
            }
        }
    }
}
