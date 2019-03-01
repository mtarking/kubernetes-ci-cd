podTemplate(label: 'docker',
  containers: [containerTemplate(name: 'docker', image: 'docker:1.11', ttyEnabled: true, command: 'cat')],
  volumes: [hostPathVolume(hostPath: '/var/run/docker.sock', mountPath: '/var/run/docker.sock')]
  ) {
    node('docker') {
        
        checkout scm

        env.DOCKER_API_VERSION="1.23"
    
        sh "git rev-parse --short HEAD > commit-id"

        stage "Build"
          container('docker'){
            sh "sleep 3600"
          }
    }
}
