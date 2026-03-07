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
                sh 'docker build -t $DOCKER_USER/backend-app:v2 backend'
                sh 'docker build -t $DOCKER_USER/frontend-app:v2 frontend'
            }
        }

        stage('Push Docker Images') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS')]) {

                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                    sh 'docker push $DOCKER_USER/backend-app:v2'
                    sh 'docker push $DOCKER_USER/frontend-app:v2'
                }
            }
        }

        stage('Run Containers') {
            steps {
                sh '''
                echo "Removing old containers..."
                docker stop backend || true
                docker rm backend || true

                docker stop frontend || true
                docker rm frontend || true

                echo "Starting backend container..."
                docker run -d --name backend -p 8085:8080 $DOCKER_USER/backend-app:v2

                echo "Starting frontend container..."
                docker run -d --name frontend -p 8084:8080 -e BACKEND=http://localhost:8085 $DOCKER_USER/frontend-app:v2
                '''
            }
        }

        stage('Verify Application') {
    steps {
        sh '''
        echo "Checking Backend..."
        for i in {1..10}
        do
            curl -I http://localhost:8085 && break
            echo "Backend not ready yet... retrying"
            sleep 5
        done

        echo "Checking Frontend..."
        for i in {1..10}
        do
            curl -I http://localhost:8084 && break
            echo "Frontend not ready yet... retrying"
            sleep 5
        done

        echo "Both Backend and Frontend are running successfully!"
        '''
    }
}
    }
}
