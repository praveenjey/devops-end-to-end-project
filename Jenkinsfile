pipeline {
    agent any

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

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t devops-app:latest .'
            }
        }

        stage('Run Container (CI Test)') {
            steps {
                sh '''
                docker run -d -p 5000:5000 devops-app:latest
                sleep 5
                curl http://localhost:5000
                docker ps -q --filter "ancestor=devops-app:latest" | xargs docker stop
                '''
            }
        }
    }
}

