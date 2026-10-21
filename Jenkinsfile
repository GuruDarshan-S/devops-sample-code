pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo 'Installing dependencies...'
                sh '''
                    python3 -m venv .venv
                    .venv/bin/pip install --upgrade pip
                    .venv/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Test') {
            steps {
                echo 'Running unit tests...'
                sh '.venv/bin/python -m unittest discover -s .'
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh '''
                    mkdir -p "${WORKSPACE}/python-app-deploy"
                    cp app.py requirements.txt "${WORKSPACE}/python-app-deploy/"
                '''
            }
        }

        stage('Run Application') {
            steps {
                echo 'Checking that the application can start...'
                sh '''
                    .venv/bin/python -c "from app import app; print('Flask application loaded successfully')"
                '''
            }
        }

        stage('Test Application') {
            steps {
                echo 'Testing the deployed application...'
                sh '''
                    cd "${WORKSPACE}/python-app-deploy"
                    ../.venv/bin/python -c "
from app import app
response = app.test_client().get('/')
assert response.status_code == 200
assert response.get_data(as_text=True) == 'Hello, Jenkins Multi-Stage Pipeline!'
print(response.get_data(as_text=True))
print('Deployed application test passed!')
"
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the console output.'
        }
    }
}
