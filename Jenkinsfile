pipeline {
    agent any

    environment {
        VENV = "venv"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'develop', url: 'https://github.com/SohamBodh7/django-todo.git'
            }
        }

        stage('Set Up Virtual Environment') {
            steps {
                sh '''
                python3 -m venv $VENV || python -m venv $VENV
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                source $VENV/bin/activate || call $VENV\\Scripts\\activate
                pip install --upgrade pip
                pip install -r requirements.txt
                '''
            }
        }

        stage('Migrate and Collect Static Files') {
            steps {
                sh '''
                source $VENV/bin/activate || call $VENV\\Scripts\\activate
                python manage.py migrate
                python manage.py collectstatic --noinput
                '''
            }
        }

        stage('Run Tests') {
            steps {
                sh '''
                source $VENV/bin/activate || call $VENV\\Scripts\\activate
                python manage.py test
                '''
            }
        }

        stage('Run Django Server') {
            steps {
                sh '''
                source $VENV/bin/activate || call $VENV\\Scripts\\activate
                nohup python manage.py runserver 0.0.0.0:8000 &
                '''
            }
        }
    }

    post {
        success {
            emailext (
                subject: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build succeeded! View logs: ${env.BUILD_URL}",
                to: "sohamdevops2025@gmail.com"
            )
        }

        failure {
            emailext (
                subject: "❌ FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build failed. Check console logs: ${env.BUILD_URL}",
                to: "sohamdevops2025@gmail.com"
            )
        }
    }
}
