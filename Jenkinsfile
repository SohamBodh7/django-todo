pipeline {
    agent any

    environment {
        VENV = "venv"
        PORT = "8000"
        HOST = "0.0.0.0"
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
                python3 -m venv $VENV || true
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                . $VENV/bin/activate
                pip install --upgrade pip
    		pip install django
                '''
            }
        }

        stage('Run Migrations & Collect Static') {
            steps {
                sh '''
                . $VENV/bin/activate
                python manage.py migrate
                python manage.py collectstatic --noinput
                '''
            }
        }

        stage('Restart Django Server') {
    	    steps {
        	sh '''
        	pkill -f "manage.py runserver" || true
        	. $VENV/bin/activate
        	nohup python manage.py runserver $HOST:$PORT > server.log 2>&1 &
        	disown
        	'''
    	   }
	}
    }

    post {
        success {
            emailext (
                subject: "✅ Django TODO Build Success: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Successfully deployed. View logs at: ${env.BUILD_URL}",
                to: "sohamdevops2025@gmail.com"
            )
        }
        failure {
            emailext (
                subject: "❌ Django TODO Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build failed. See console logs: ${env.BUILD_URL}",
                to: "sohamdevops2025@gmail.com"
            )
        }
    }
}

