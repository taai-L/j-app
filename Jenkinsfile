#!groovy
// Run docker build
properties([disableConcurrentBuilds()])

pipeline {
    agent { 
        label 'ubuntu'
        }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5', artifactNumToKeepStr: '5'))
        timestamps()
    }
    stages {
        stage("create docker image") {
            steps {
                echo " ================ start building image ================="
                	sh '''
                    cd /home/ubuntu/jenkins/workspace/own-pp/cont/
                    docker build -t 0686519782/nginx-test:1.0.$BUILD_NUMBER . 
                    '''
            }
        }
        stage("docker login") {
            steps {
                echo " =================== docker login ==================="
                withCredentials([usernamePassword(credentialsId: 'dockerhub_own', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    sh 'docker login -u $USERNAME -p $PASSWORD'
                }
            }
        }
        stage("docker push") {
            steps {
                echo " ============== start pushing image =================="
                sh 'docker push 0686519782/nginx-test:1.0.$BUILD_NUMBER'
            }
        }
        stage("deploy") {
            steps {
                echo " ================== start deploying =================="
                sh '''
                eval `ssh-agent -s`
                ssh-agent $BASH
                ssh-add ~/.ssh/A2.pem
                ssh ubuntu@174.129.60.93 "sh kill.sh; sh docker_rm.sh; \
                docker run -d --name nginx-t -p 8090:80 0686519782/nginx-test:1.0.$BUILD_NUMBER"
                echo $BUILD_NUMBER > bn_own-pp
                '''
            }
        }
    }
}
