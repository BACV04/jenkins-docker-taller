pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'bacv04/fastapi-ejemplo'
        DOCKERHUB_CREDENTIALS = 'dockerhub-credentials-id'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/BACV04/jenkins-docker-taller.git' // Reemplaza si usas otro repo
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    try {
                        sh './venv/bin/pip install -r requirements.txt'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }

        stage('Run Unit Tests') {
            steps {
                script {
                    try {
                        sh '. venv/bin/activate && pytest'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    try {
                        sh 'docker build -t $DOCKER_IMAGE .'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                script {
                    try {
                        withCredentials([usernamePassword(credentialsId: "$DOCKERHUB_CREDENTIALS", usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_PASS')]) {
                            sh 'echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin'
                        }
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    try {
                        sh 'docker push $DOCKER_IMAGE'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                        throw e
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline completed"
        }
        success {
            echo "Build and push to DockerHub was successful!"
        }
        failure {
            echo "Build or push failed!"
        }
    }
}
