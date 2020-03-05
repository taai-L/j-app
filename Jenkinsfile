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
                echo " ==================== git clone ===================="
                sh '''
                cd /home/ubuntu/git
                rm -rf j-app/
                git clone git@github.com:taai-L/j-app.git
                '''
                }
            }
        stage("create docker image") {
            steps {
                echo " ============== start building image =================="
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
                ansible staging_servers -m shell -a "docker pull 0686519782/nginx-test:1.0.$BUILD_NUMBER"
                ansible staging_servers -m shell -a "sh kill.sh"
                ansible staging_servers -m shell -a "sh docker_rm.sh"
                ansible staging_servers -m shell -a "docker run -d --name nginx-t01 -p 8090:80 0686519782/nginx-test:1.0.$BUILD_NUMBER"
                '''
            }
        }
    }
}
