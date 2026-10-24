pipeline{
    agent any
    environment{
            IMAGE_NAME='tanay25/flask-jenkins-demo'
    }
    stages{
        stage('Checkout'){
            steps{
               checkout scm
            }
        }
        
        stage("Setup"){
            steps{
                sh '''
                    python3 -m venv .venv
                    .venv/bin/python -m pip install --upgrade pip
                    .venv/bin/python -m pip install -r requirements.txt 
                '''
            }
        }
        
        stage('Build'){
            steps{
                sh '''
                    docker build -t $IMAGE_NAME:$BUILD_NUMBER -t $IMAGE_NAME:latest .
                '''
            }
        }
        
        stage('Push to Docker Hub'){
            steps{
                withCredentials([
                    usernamePassword(credentialId:'dockerhub')
                    docekr push $IMAGE_NAME:$BUILD_NUMBER
                    docekr push $IMAGE_NAME:latest
                ])
            }
        }
        stage('Test Docker Container'){
            steps{
                sh '''
                    docker rm -f flask-test || true
                    docker run -d --name flask-test $IMAGE_NAME:$BUILD_NUMBER
                '''
            }
        }
    }

}