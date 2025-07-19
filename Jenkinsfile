pipeline {
    agent any
 
    environment {
        IMAGE_NAME = 'docker-app'
        DOCKER_HUB_REPO = 'shalini103/docker-app'
    }
 
    stages {
        stage('Clone Repo') {
            steps {
                git credentialsId: "${git-creds}", url: 'https://github.com/bishtshalini/git-1-nbn.git', branch: 'main'
            }
        }
 
        stage('Trivy Scan') {
            steps {
                sh 'trivy fs --exit-code 0 --severity MEDIUM,HIGH .'
            }
        }
 
        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME ."
            }
        }
 
        stage('Run Container (Local Test)') {
            steps {
                sh "docker stop nbn-container || true"
                sh "docker rm nbn-container || true"
                sh "docker run -d -p 5002:5002 --name nbn-container $IMAGE_NAME"
            }
        }
 
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    sh "docker tag $IMAGE_NAME $DOCKER_HUB_REPO"
                    sh "docker push $DOCKER_HUB_REPO"
                }
            }
        }
    }
}


