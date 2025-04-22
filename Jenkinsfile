pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'bacv04/fastapi-ejemplo'
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials-id'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/BACV04/jenkins-docker-taller.git' // Reemplaza si usas otro repo
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'python -m venv venv && . venv/bin/activate && pip install -r requirements.txt'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh '. venv/bin/activate && pytest'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Login to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: "$DOCKERHUB_CREDENTIALS", usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh 'echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin'
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh 'docker push $DOCKER_IMAGE'
            }
        }
    }
}
