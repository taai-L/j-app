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
        stage("git clone") {
            steps {
                echo " ===================== git clone ====================="
                sh '''
                cd /home/ubuntu/git
                rm -rf j-app/
                git clone git@github.com:taai-L/j-app.git
                '''
                }
            }
        stage("create docker image") {
            steps {
                echo " ================ start building image ================="
                	sh '''
                    cd /home/ubuntu/git/j-app/cont/
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
                ssh-add -l
                ssh -tt ubuntu@174.129.60.93 "docker pull 0686519782/nginx-test:1.0.$BUILD_NUMBER"
                ssh -tt ubuntu@174.129.60.93 "curl ifconfig.me"
                ssh -tt ubuntu@174.129.60.93 "docker kill $(docker ps -q)"
                ssh -tt ubuntu@174.129.60.93 "docker rm $(docker ps -qa)"
                ssh -tt ubuntu@174.129.60.93 "docker run -d --name nginx-t -p 8090:80 0686519782/nginx-test:1.0.$BUILD_NUMBER"
                '''
            }
        }
    }
}
