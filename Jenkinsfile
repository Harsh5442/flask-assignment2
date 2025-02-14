pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "harshpatel5442/flask_app"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'github-ssh-key', url: 'https://github.com/Harsh5442/flask-assignment2.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'docker run --rm %DOCKER_IMAGE% python -m unittest discover'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub_credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat '''
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    docker push %DOCKER_IMAGE%
                    '''
                }
            }
        }

        stage('Deploy Application') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'remote-server-ssh', keyFileVariable: 'SSH_KEY')]) {
                    bat '''
                    echo Deploying to server...
                    plink -ssh -i %SSH_KEY% user@yourserver "docker pull %DOCKER_IMAGE% && docker-compose up -d"
                    '''
                }
            }
        }
    }
}
