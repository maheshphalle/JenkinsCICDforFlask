pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '''
                echo "Checking Python Version..."
                python3 --version

                echo "Checking if pipx is installed..."
                python3 -m pipx --version || echo "pipx is not installed!"

                echo "Installing pipx..."
                python3 -m ensurepip --default-pip || true
                python3 -m pip install --user pipx

                echo "Ensuring pipx is set up..."
                python3 -m pipx ensurepath

                echo "Installing pip via pipx..."
                pipx install pip

                echo "Installing dependencies..."
                pipx runpip pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                echo "Running tests..."
                pipx runpip pip pytest || echo "Tests failed!"
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
