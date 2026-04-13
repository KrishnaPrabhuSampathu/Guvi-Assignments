pipeline {
    agent any

    environment {
        DOCKER_USER = "rajkumartst"
        DEV_IMAGE = "${DOCKER_USER}/react-app-dev"
        PROD_IMAGE = "${DOCKER_USER}/react-app-prod"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/sriram-R-krishnan/devops-build.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t react-app .'
            }
        }

        stage('Push to Dev Repo') {
            when {
                branch 'dev'
            }
            steps {
                sh 'docker tag react-app $DEV_IMAGE'
                sh 'docker push $DEV_IMAGE'
            }
        }

        stage('Push to Prod Repo') {
            when {
                branch 'main'
            }
            steps {
                sh 'docker tag react-app $PROD_IMAGE'
                sh 'docker push $PROD_IMAGE'
            }
        }

        stage('Deploy to Server') {
            when {
                branch 'main'
            }
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@<APP_SERVER_IP> << EOF
                    docker pull $PROD_IMAGE
                    docker stop react-container || true
                    docker rm react-container || true
                    docker run -d -p 80:80 --name react-container $PROD_IMAGE
                    EOF
                    '''
                }
            }
        }
    }
}