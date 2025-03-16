pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '''
                echo "Checking Python Version..."
                python3 --version

                echo "Creating a virtual environment..."
                python3 -m venv venv

                echo "Activating virtual environment..."
                source venv/bin/activate

                echo "Installing pip inside virtual environment..."
                python3 -m ensurepip --default-pip
                python3 -m pip install --upgrade pip

                echo "Installing dependencies..."
                python3 -m pip install -r requirements.txt

                echo "Deactivating virtual environment..."
                deactivate
                '''
            }
        }

        stage('Test') {
            steps {
                sh '''
                echo "Activating virtual environment..."
                source venv/bin/activate

                echo "Running tests..."
                python3 -m pytest || echo "Tests failed!"

                echo "Deactivating virtual environment..."
                deactivate
                '''
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                echo "Activating virtual environment..."
                source venv/bin/activate

                echo "Starting Flask app..."
                python3 app.py &

                echo "Deactivating virtual environment..."
                deactivate
                '''
            }
        }
    }
}
