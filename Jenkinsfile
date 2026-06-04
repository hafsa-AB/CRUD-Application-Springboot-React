pipeline {
    agent any
    
    environment {
        DOCKER_HUB_USER = 'hafsa-ab'
        BACKEND_IMAGE = "${DOCKER_HUB_USER}/crud-backend"
        FRONTEND_IMAGE = "${DOCKER_HUB_USER}/crud-frontend"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'develop',
                    url: 'https://github.com/hafsa-AB/CRUD-Application-Springboot-React.git'
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend/crudfront') {
                    sh 'npm install && npm run build'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t ${BACKEND_IMAGE} ./backend'
                sh 'docker build -t ${FRONTEND_IMAGE} ./frontend/crudfront'
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push ${BACKEND_IMAGE}'
                    sh 'docker push ${FRONTEND_IMAGE}'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f kubernetes/'
            }
        }
    }

    post {
        success {
            echo 'Pipeline terminé avec succès !'
        }
        failure {
            echo 'Pipeline échoué !'
        }
    }
}
