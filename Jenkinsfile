pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKER_REGISTRY = 'mydockerhubusername/flask-app-example'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    def branch = env.BRANCH_NAME ?: 'main'
                    git branch: branch, url: 'https://github.com/Honoga-Sensei/formazione_sou_k8s.git'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    def tag = ''
                    if (env.BRANCH_NAME == 'master' || env.BRANCH_NAME == 'main') {
                        tag = 'latest'
                    } else if (env.BRANCH_NAME == 'develop') {
                        tag = "develop-${env.GIT_COMMIT[0..6]}"
                    } else if (env.GIT_TAG_NAME) {
                        tag = env.GIT_TAG_NAME
                    }

                    docker.build("${DOCKER_REGISTRY}:${tag}")
                }
            }
        }

        stage('Push') {
            steps {
                script {
                    docker.withRegistry('', 'dockerhub-credentials') {
                        def tag = ''
                        if (env.BRANCH_NAME == 'master' || env.BRANCH_NAME == 'main') {
                            tag = 'latest'
                        } else if (env.BRANCH_NAME == 'develop') {
                            tag = "develop-${env.GIT_COMMIT[0..6]}"
                        } else if (env.GIT_TAG_NAME) {
                            tag = env.GIT_TAG_NAME
                        }

                        docker.image("${DOCKER_REGISTRY}:${tag}").push()
                    }
                }
            }
        }
    }
}
