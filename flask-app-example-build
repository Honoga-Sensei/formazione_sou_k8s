def buildAndPushTag(Map args) {
    def defaults = [
        registryUrl: '***',
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
