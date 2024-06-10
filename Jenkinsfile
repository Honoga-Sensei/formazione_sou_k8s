pipeline {
    environment {
        imagename = "honogasensei/formazione_sou"
        registryCredential = 'DockerHub'
        dockerImage = ''
    }
    agent any
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Cloning Git') {
            steps {
                script {
                    // Clona il repository senza specificare un branch fisso
                    checkout scm
                    // Ottieni l'ultimo tag Git disponibile
                    env.GIT_TAG = sh(script: 'git describe --tags --abbrev=0 || echo ""', returnStdout: true).trim()
                    // Ottieni il nome del branch
                    env.BRANCH_NAME = env.GIT_BRANCH.replaceAll('origin/', '')
                    echo "Cloned Branch: ${env.BRANCH_NAME}"
                    echo "Git Tag: ${env.GIT_TAG}"
                }
            }
        }
        stage('Debug Info') {
            steps {
                script {
                    echo "Branch Name: ${env.BRANCH_NAME}"
                    echo "Git Commit: ${env.GIT_COMMIT}"
                    echo "Git Tag: ${env.GIT_TAG}"
                }
            }
        }
        stage('Building image') {
            steps {
                script {
                    dockerImage = docker.build("${imagename}:${env.GIT_COMMIT}")
                }
            }
        }
        stage('Deploy Image') {
            steps {
                script {
                    def tag = ""
                    def additionalTag = ""
                    if (env.GIT_TAG && env.GIT_TAG != "") {
                        tag = env.GIT_TAG
                        additionalTag = 'latest'
                    } else if (env.BRANCH_NAME == 'main') {
                        tag = 'latest'
                    } else if (env.BRANCH_NAME == 'secondary') {
                        tag = "secondary-${env.GIT_COMMIT}"
                    } else {
                        tag = "${env.BRANCH_NAME}-${env.GIT_COMMIT}"
                    }
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push(tag)
                        if (additionalTag) {
                            dockerImage.push(additionalTag)
                        }
                    }
                }
            }
        }
        stage('Remove Unused docker image') {
            steps {
                script {
                    def tag = ""
                    def additionalTag = ""
                    if (env.GIT_TAG && env.GIT_TAG != "") {
                        tag = env.GIT_TAG
                        additionalTag = 'latest'
                    } else if (env.BRANCH_NAME == 'main') {
                        tag = 'latest'
                    } else if (env.BRANCH_NAME == 'secondary') {
                        tag = "secondary-${env.GIT_COMMIT}"
                    } else {
                        tag = "${env.BRANCH_NAME}-${env.GIT_COMMIT}"
                    }
                    sh "docker rmi ${imagename}:${env.GIT_COMMIT}"
                    sh "docker rmi ${imagename}:${tag}"
                    if (additionalTag) {
                        sh "docker rmi ${imagename}:${additionalTag}"
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
