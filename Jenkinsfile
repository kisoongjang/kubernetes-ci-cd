containers: [
	  containerTemplate(name: 'maven', image: 'maven:3.3.9-jdk-8-alpine', ttyEnabled: true, command: 'cat'),
	  containerTemplate(name: 'docker', image: 'docker:stable', ttyEnabled: true, command: 'cat'),
	  containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.8.0', command: 'cat', ttyEnabled: true)
	],
	volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]
	) {

node {

    checkout scm

    env.DOCKER_API_VERSION="1.40"
    
    sh "git rev-parse --short HEAD > commit-id"

    tag = readFile('commit-id').replace("\n", "").replace("\r", "")
    appName = "hello-kenzan"
    registryHost = "127.0.0.1:5000/"
    imageName = "${registryHost}${appName}:${tag}"
    env.BUILDIMG=imageName

    stage "Build"
    
        sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"
    
    stage "Push"

        sh "docker push ${imageName}"

    stage "Deploy"

        kubernetesDeploy configs: "applications/${appName}/k8s/*.yaml", kubeconfigId: 'kenzan_kubeconfig'

}
    }
