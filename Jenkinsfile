podTemplate(label: 'docker',
  containers: [containerTemplate(name: 'docker', image: 'docker:1.11', ttyEnabled: true, command: 'cat')],
  volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]
  ) {
    node {
        
        checkout scm

        env.DOCKER_API_VERSION="1.23"
    
        sh "git rev-parse --short HEAD > commit-id"

        tag = readFile('commit-id').replace("\n", "").replace("\r", "")
        appName = "hello-kenzan"
        registryHost = "10.100.9.29/"
        project = "ciscolive/"
        imageName = "${registryHost}${project}${appName}:${tag}"
        env.BUILDIMG=imageName

        stage "Build"

            sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"

        stage "Push"

            sh "docker push ${imageName}"

        stage "Deploy"

            kubernetesDeploy configs: "applications/${appName}/k8s/*.yaml", kubeconfigId: 'kenzan_kubeconfig'
    
    }
}
