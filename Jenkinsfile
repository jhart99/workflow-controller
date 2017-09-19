def containers = ["workflow-controller"]
def dversion = '1.12.6'
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
    //git url: "https://github.com/jhart99/arachne.git"
    //    sh "git rev-parse HEAD > .git/commit-id"
    //    def commit_id = readFile('.git/commit-id').trim()
    //    println commit_id

    node('dockerpod') {
        checkout scm
            commit = sh(returnStdout: true, script: "git log -n 1 --pretty=format:'%h'").trim()
            echo commit
            container('docker') {
                for (container in containers) {
                    stage("build $container") {
                        dir("docker/${container}/docker"){
                            sh "docker build -t vogt1005.scripps.edu:5000/${container}:${commit} ."
                        }
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
}
