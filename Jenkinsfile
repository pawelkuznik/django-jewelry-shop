pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'pip3 install -r requirements.txt'
            }
        }
        stage('Application unit Tests') {
            steps {
                sh 'python3 manage.py test'
            }
        }
        stage('Deploy to staging') {
            steps {
                sh 'ssh -o StrictHostKeyChecking=no deployment-user@3.72.154.24 "source venv/bin/activate; \
                cd django-jewelry-shop; \
                git pull origin main; \
                pip install -r requirements.txt --no-warn-script-location; \
                python manage.py migrate; \
                deactivate; \
                sudo systemctl restart nginx; \
                sudo systemctl restart gunicorn "'
            }
        }
        stage('visual regression tests') {
            steps {
                sh 'ssh -o StrictHostKeyChecking=no deployment-user@3.72.154.24 "source venv/bin/activate; \
                cd django-jewelry-shop; \
                git pull origin main; \
                docker pull backstopjs/backstopjs; \
                cd tests/visual_regression_tests; \
                pwd; \
                docker run --rm -v $(pwd):/src backstopjs/backstopjs reference; \
                deactivate"'
            }
        }
        stage('Deploy to production') {
            input {
            message "Shall we deploy to production?"
            ok "Yes, please"
            }
            steps {
                sh 'ssh -o StrictHostKeyChecking=no deployment-user@52.58.220.246 "source venv/bin/activate; \
                cd django-jewelry-shop; \
                git pull origin main; \
                pip install -r requirements.txt --no-warn-script-location; \
                python manage.py migrate; \
                deactivate; \
                sudo systemctl restart nginx; \
                sudo systemctl restart gunicorn "'
            }
        }
    }
}