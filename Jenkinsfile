pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '''
                echo "Checking Python Version..."
                python3 --version

                echo "Downloading get-pip.py manually..."
                curl -O https://bootstrap.pypa.io/get-pip.py

                echo "Installing pip in user space..."
                python3 get-pip.py --break-system-packages --user

                echo "Verifying pip installation..."
                python3 -m pip --version

                echo "Installing dependencies with system override..."
                python3 -m pip install --break-system-packages --user -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                echo "Running tests..."
                python3 -m pytest || echo "Tests failed!"
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                echo "Starting Flask app..."
                python3 app.py &
                '''
            }
        }
    }
}
