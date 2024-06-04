pipeline {
    agent any

    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-credentials')
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
                    def buildTag = ""
                    if (env.GIT_BRANCH == 'main') {
                        buildTag = "latest"
                    } else if (env.GIT_BRANCH == 'develop') {
                        buildTag = "develop-${env.GIT_COMMIT}"
                    } else if (env.GIT_TAG) {
                        buildTag = env.GIT_TAG
                    } else {
                        buildTag = "latest"
                    }

                    buildAndPushTag(
                        registryUrl: "https://hub.docker.com/repository/docker/honogasensei/formazione_sou/general",
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
        registryUrl: 'https://hub.docker.com/repository/docker/honogasensei/formazione_sou/general',
        dockerfileDir: "./",
        dockerfileName: "Dockerfile",
        buildArgs: "",
        pushLatest: true
    ]
    args = defaults + args
    docker.withRegistry(args.registryUrl) {
        def image = docker.build(args.image, "${args.buildArgs} ${args.dockerfileDir} -f ${args.dockerfileName}")
        image.push(args.buildTag)
        if(args.pushLatest) {
            image.push("latest")
            sh "docker rmi --force ${args.image}:latest"
        }
        sh "docker rmi --force ${args.image}:${args.buildTag}"
 
        return "${args.image}:${args.buildTag}"
    }
}
