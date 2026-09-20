pipeline {

    agent any

    triggers {
        // Check GitHub for changes automatically
        pollSCM('H/2 * * * *')
    }

    stages {

        stage('Install Dependencies') {
            steps {
                sh '''
                    python3 -m venv venv
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

        stage('Stop Previous Flask App') {
            steps {
                sh '''
                    if [ -f flask.pid ]; then
                        OLD_PID=$(cat flask.pid)

                        if kill -0 $OLD_PID 2>/dev/null; then
                            echo "Stopping previous Flask process: $OLD_PID"
                            kill $OLD_PID || true
                            sleep 2
                        fi

                        rm -f flask.pid
                    fi
                '''
            }
        }

        stage('Start Flask Application') {
            steps {
                sh '''
                    nohup venv/bin/python app.py > flask.log 2>&1 &
                    echo $! > flask.pid

                    echo "Flask started with PID $(cat flask.pid)"

                    sleep 5
                '''
            }
        }

        stage('Verify Flask Application') {
            steps {
                sh '''
                    curl -f http://127.0.0.1:5000/
                    echo ""
                    echo "Flask application is running successfully!"
                '''
            }
        }
    }

    post {

        success {
            echo 'Flask CI Pipeline completed successfully!'
            echo 'Application is running on port 5000.'
        }

        failure {
            echo 'Flask CI Pipeline failed.'
        }
    }
}
