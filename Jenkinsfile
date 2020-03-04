#!groovy
// Run docker build
properties([disableConcurrentBuilds()])

pipeline {
    agent { 
        label 'master'
        }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
        timestamps()
    }
    stages {
        stage("git clone") {
            steps {
                echo " ============== git clone =================="
                sh """
                cd /var/lib/jenkins/git
                rm -rf j-app/
                git clone git@github.com:taai-L/j-app.git
                """
                }
            }
        stage("docker login") {
            steps {
                echo " ============== docker login =================="
                withCredentials([usernamePassword(credentialsId: 'dockerhub_own', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh """
                    docker login -u $USERNAME -p $PASSWORD
                    """
                }
            }
        }
        stage("create docker image") {
            steps {
                echo " ============== start building image =================="
                dir ('html/') {
                	sh 'docker build -t 0686519782/nginx-test:1.0.$BUILD_NUMBER . '
                }
            }
        }
        stage("docker push") {
            steps {
                echo " ============== start pushing image =================="
                sh '''
                docker push 0686519782/nginx-test:1.0.$BUILD_NUMBER
                '''
            }
        }
    }
}
