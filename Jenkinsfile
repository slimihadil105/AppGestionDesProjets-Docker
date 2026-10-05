pipeline {
    agent any

    environment {
        DOCKER_USER    = 'hadilslimi'
        BACKEND_IMAGE  = "${DOCKER_USER}/projets-backend"
        FRONTEND_IMAGE = "${DOCKER_USER}/projets-frontend"
        TAG            = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build images') {
            steps {
                sh 'docker build -t $BACKEND_IMAGE:$TAG -t $BACKEND_IMAGE:latest ./backend'
                sh 'docker build -t $FRONTEND_IMAGE:$TAG -t $FRONTEND_IMAGE:latest ./frontend'
            }
        }

        stage('Push images') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                                 usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh 'echo $DH_PASS | docker login -u $DH_USER --password-stdin'
                    sh 'docker push $BACKEND_IMAGE:$TAG'
                    sh 'docker push $BACKEND_IMAGE:latest'
                    sh 'docker push $FRONTEND_IMAGE:$TAG'
                    sh 'docker push $FRONTEND_IMAGE:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                sh 'docker compose down || true'
                sh 'docker compose up -d --build'
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}
