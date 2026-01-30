pipeline {
    agent any

    environment {
        IMAGE_NAME = "praveenjey24/devops-app"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM',
                  branches: [[name: '*/main']],
                  userRemoteConfigs: [[
                    url: 'https://github.com/praveenjey/devops-end-to-end-project.git',
                    credentialsId: 'github-token'
                  ]]
                ])
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('CI Test') {
            steps {
                sh '''
                docker run -d -p 5000:5000 $IMAGE_NAME:latest
                sleep 5
                curl http://localhost:5000
                docker ps -q --filter "ancestor=$IMAGE_NAME:latest" | xargs docker stop
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME:latest'
            }
        }
    }
}

