pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    stages {
        stage('checkout'){
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/ssukumar27/ownk8sproject.git']]])
            }
        }
        stage('build') {
            steps {
                sh 'mvn clean install -f pom.xml'
            }
        }
        stage('build docker image') {
            steps {
                script {
                    sh 'sudo docker build -t newimage /var/lib/jenkins/workspace/own'
                    sh 'sudo docker tag newimage ssukumar27/newimage:latest'
                    sh 'sudo docker tag newimage ssukumar27/newimage:${BUILD_NUMBER}'
                }
            }
        }
        stage('login & push to docker'){
            steps {
                withCredentials([string(credentialsId: 'docker_hub', variable: 'docker_hub')]) {
                    sh 'sudo docker login -u ssukumar27 -p ${docker_hub}'
                }
                sh 'sudo docker image push ssukumar27/newimage:latest'
                sh 'sudo docker image push ssukumar27/newimage:${BUILD_NUMBER}'
            }
        }
        stage('Deploy on Kubernetes') {
            steps {
                sshagent(['3.86.208.200']) {
                sh 'sudo kubectl apply -f /var/lib/jenkins/workspace/own/deployment.yml'
                }
            }
        }
    }
}
