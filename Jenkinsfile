pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "yourdockerhubusername/flask_app"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git credentialsId: 'github-ssh-key', url: 'git@github.com:yourusername/your-repo.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'docker run --rm $DOCKER_IMAGE python -m unittest discover'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }

        stage('Deploy Application') {
            steps {
                sshagent(['remote-server-ssh']) {
                    sh '''
                    ssh user@yourserver "docker pull $DOCKER_IMAGE && docker-compose up -d"
                    '''
                }
            }
        }
    }
}
