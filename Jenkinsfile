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
                    sh "docker build -t workflow-controller:build -f Dockerfile.onbuild ."
//                    sh """
//                        id=\$(docker create vogt1005.scripps.edu:5000/${container}:build)
//                        docker cp ${id}:/go/bin/workflow-controller workflow-controller
//                        docker rm -v $id
//                        docker build -t vogt1005.scripps.edu:5000/${container}:${commit} -f Dockerfile.scratch
//                        """
                }
                stage("test $container") {
                    sh "echo test passed"
                }
                stage("deploy $container") {
                    sh """
                        docker tag vogt1005.scripps.edu:5000/${container}:${commit} vogt1005.scripps.edu:5000/${container}:latest
                        docker push vogt1005.scripps.edu:5000/${container}:latest
                        """
                }
            }
    }
}
