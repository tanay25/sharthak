
pipeline {
    agent any

    environment {
        IMAGE_NAME = 'tanay25/flask-jenkins-demo'
    }

    stages {
        stage('Connection to Git') {
            steps {
                checkout scm
            }
        }

        stage('Setup Python') {
            steps {
                sh '''
                    python3 -m venv .venv
                    .venv/bin/python -m pip install --upgrade pip
                    .venv/bin/python -m pip install -r requirements.txt
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                    docker build \
                        -t $IMAGE_NAME:$BUILD_NUMBER \
                        -t $IMAGE_NAME:latest .
                '''
            }
        }

        stage('Test Docker Container') {
            steps {
                sh '''
                    docker rm -f flask-test || true

                    docker run -d \
                        --name flask-test \
                        $IMAGE_NAME:$BUILD_NUMBER

                '''
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKERHUB_PASSWORD" |
                            docker login \
                                -u "$DOCKERHUB_USER" \
                                --password-stdin

                        docker push $IMAGE_NAME:$BUILD_NUMBER
                        docker push $IMAGE_NAME:latest

                        docker logout
                    '''
                }
            }
        }
    }

   
}
