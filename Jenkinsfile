def dversion = '1.12.6'
def container = 'workflow-controller'
podTemplate(label: 'dockerpod', containers: [
        containerTemplate(name: 'docker',
            image: "docker:${dversion}",
            ttyEnabled: true,
            command: '/bin/sh -c', 
            args: 'cat'),
        containerTemplate(name: 'jnlp',
            image: 'jenkinsci/jnlp-slave',
            args: '${computer.jnlpmac} ${computer.name}')
    ], volumes: [
        hostPathVolume(hostPath: '/var/run/docker.sock',
            mountPath: '/var/run/docker.sock')]
        ) {
    node('dockerpod') {
        checkout scm
            commit = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%h'").trim()
            echo commit
            container('docker') {
                stage("build workflow-controller") {
                    sh(script: "docker build -t workflow-controller:build -f Dockerfile.onbuild .")
                }
            }
    }
}
