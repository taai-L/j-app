#!groovy
properties([disableConcurrentBuilds()])

pipeline {
    agent {
        label 'master'
    }
    options {
        buildDiscarber(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
        timestamps()
    }
    stages ("create docker image") {
        steps {
            echo "========== start building image =========="
            dir ('docker') {
                sh 'docker build .'
            }
        }
    }
}
