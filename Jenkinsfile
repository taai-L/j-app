#!groovy
// Run docker build
properties([disableConcurrentBuilds()])

pipeline {
    agent { 
        label 'master'
        }
    triggers { pollSCM('* * * * *') }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
        timestamps()
    }
    stages {
        stage("create docker image") {
            steps {
                echo " ============== start building image =================="
                dir ('docker/toolbox') {
                	sh 'docker build . '
                }
            }
        }
    }
}
