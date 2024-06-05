ipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('DockerHub')
        DOCKERHUB_REPO = 'ericawanja/todoapp'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DockerHub', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'echo $PASSWORD | docker login -u $USERNAME --password-stdin'
                }
            }
        }
        stage('Build') {
            steps {
                sh "docker build -t ${DOCKERHUB_REPO}:latest ."
            }
        }
        stage('Push') {
            steps {
                sh "docker push ${DOCKERHUB_REPO}:latest"
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}
