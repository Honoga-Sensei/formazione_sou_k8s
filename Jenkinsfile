pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('DockerHub')
        DOCKER_REGISTRY_URL = 'https://hub.docker.com/repository/docker/honogasensei/formazione_sou'
        DOCKERHUB_REPO = 'honogasensei/formazione_sou'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def buildTag = env.GIT_TAG ?: (env.BRANCH_NAME == 'main' ? "latest" : (env.BRANCH_NAME == 'develop' ? "develop-${env.GIT_COMMIT}" : "latest"))

                    buildAndPushTag(
                        registryUrl: "${DOCKER_REGISTRY_URL}",
                        image: "${DOCKERHUB_REPO}",
                        buildTag: buildTag,
                        dockerfileDir: ".",
                        dockerfileName: "Dockerfile",
                        buildArgs: ""
                    )
                }
            }
        }
    }
}

def buildAndPushTag(Map args) {
    def defaults = [
        registryUrl: "${DOCKER_REGISTRY_URL}",
        dockerfileDir: "./",
        dockerfileName: "Dockerfile",
        buildArgs: "",
        pushLatest: true
    ]
    args = defaults + args
    docker.withRegistry(args.registryUrl, DOCKER_HUB_CREDENTIALS) {
        def image = docker.build(args.image, "-f ${args.dockerfileDir}/${args.dockerfileName} ${args.buildArgs} ${args.dockerfileDir}")
        image.push(args.buildTag)
        if(args.pushLatest) {
            image.push("latest")
            sh "docker rmi --force ${args.image}:latest"
        }
        sh "docker rmi --force ${args.image}:${args.buildTag}"
    }
}
