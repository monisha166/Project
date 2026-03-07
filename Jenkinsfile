pipeline {
    agent any

    environment {
        DOCKER_USER = "monisha166"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/monisha166/Project.git'
            }
        }

        stage('Build Backend') {
            steps {
                dir('backend') {
                    sh 'mvn clean install'
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'mvn clean install'
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t $DOCKER_USER/backend-app:v1 backend'
                sh 'docker build -t $DOCKER_USER/frontend-app:v1 frontend'
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS')]) {

                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $DOCKER_USER/backend-app:v1'
                    sh 'docker push $DOCKER_USER/frontend-app:v1'
                }
            }
        }

        stage('Run Containers') {
            steps {

                sh 'docker rm -f backend || true'
                sh 'docker rm -f frontend || true'

                sh 'docker run -d --name backend -p 8085:8080 $DOCKER_USER/backend-app:v1'

                sh 'docker run -d -p 8084:8080 -e BACKEND=http://localhost:8085 --name frontend $DOCKER_USER/frontend-app:v1'
            }
        }
    }
}
