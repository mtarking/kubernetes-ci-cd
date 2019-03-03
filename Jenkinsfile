podTemplate(
  label: 'docker',
  containers: [
    containerTemplate(name: 'docker', image: 'docker:18.09.3', ttyEnabled: true, command: 'cat'), 
    containerTemplate(name: 'kubectl', image: 'lachlanevenson/k8s-kubectl:v1.12.6', command: 'cat', ttyEnabled: true),
  ],
  volumes: [
    hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')
  ]
) {
    node('docker') {
      checkout scm
      env.DOCKER_API_VERSION="1.23"
      sh "git rev-parse --short HEAD > commit-id"
      tag = readFile('commit-id').replace("\n", "").replace("\r", "")
      appName = "hello-kenzan"
      registryHost = "10.100.13.51/"
      project = "ciscolive/"
      imageName = "${registryHost}${project}${appName}:${tag}"
      env.BUILDIMG=imageName
      
      stage "Build"
      container('docker'){
        sh "docker build -t ${imageName} -f applications/hello-kenzan/Dockerfile applications/hello-kenzan"
      }
      
      stage "Push"
      container('docker'){
        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'jenkins-harbor', usernameVariable: 'DOCKER_HUB_USER', passwordVariable: 'DOCKER_HUB_PASSWORD']]){
          sh "docker login -u ${env.DOCKER_HUB_USER} -p ${env.DOCKER_HUB_PASSWORD} 10.100.13.51"
          sh "docker push ${imageName}"
        }
      }

      stage "Deploy"
      container('kubectl'){
        withCredentials([[$class: 'sshUserPrivateKey', credentialsId: 'ccpuser', usernameVariable: 'CCP_USER', keyFileVariable: 'CCP_USER_PRIVATE_KEY']]){
          sh "kubectl get nodes"
        }
      }
    }
}
