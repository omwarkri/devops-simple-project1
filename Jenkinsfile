pipeline {
    agent {
        docker { image 'docker:24-dind' } // Docker-in-Docker
    }
    environment {
        IMAGE_NAME = "omwarkri123/devops-demo"
        IMAGE_TAG  = "latest"
    }
    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/omwarkri/devops-simple-project1.git'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME:$IMAGE_TAG ."
            }
        }
        stage('Push Image to DockerHub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $IMAGE_NAME:$IMAGE_TAG
                    '''
                }
            }
        }
        stage('Deploy Container') {
            steps {
                sh '''
                docker rm -f demo-app || true
                docker run -d --name demo-app -p 5000:5000 $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }
}
