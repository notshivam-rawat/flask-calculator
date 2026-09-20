pipeline {

    agent any

    stages {

        stage('Clone Repository') {
            steps {
                echo 'Cloning Flask application from GitHub...'
            }
        }

        stage('Create Virtual Environment') {
            steps {
                sh '''
                    python3 -m venv venv
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                    venv/bin/pip install --upgrade pip
                    venv/bin/pip install -r requirements.txt
                '''
            }
        }

        stage('Test Flask Application') {
            steps {
                sh '''
                    venv/bin/python -c "from app import app; print('Flask application loaded successfully')"
                '''
            }
        }

        stage('Run Flask Application') {
            steps {
                sh '''
                    nohup venv/bin/python app.py > flask.log 2>&1 &
                    echo $! > flask.pid
                    sleep 5
                '''
            }
        }

        stage('Verify Flask Application') {
            steps {
                sh '''
                    curl -f http://127.0.0.1:5000/
                '''
            }
        }
    }

    post {

        always {
            sh '''
                if [ -f flask.pid ]; then
                    kill $(cat flask.pid) || true
                    rm -f flask.pid
                fi
            '''
        }

        success {
            echo 'Flask CI Pipeline completed successfully!'
        }

        failure {
            echo 'Flask CI Pipeline failed!'
        }
    }
}
