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

                echo "Downloading get-pip.py..."
                curl -sS https://bootstrap.pypa.io/get-pip.py -o get-pip.py

                echo "Installing pip manually in user space..."
                python3 get-pip.py --user

                echo "Verifying pip installation..."
                python3 -m pip --version

                echo "Installing dependencies..."
                python3 -m pip install --user -r requirements.txt
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
