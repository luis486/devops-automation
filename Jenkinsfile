pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    stages {
        stage('Build Maven') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/luis486/devops-automation']]])
                sh 'mvn clean install'
            }
        }
        stage('Build docker image') {
            steps {
                script{
                    sh 'docker build -t luis486/devops-integration .'
                }
            }
        }
        stage('Docker push'){
            steps {
                script{
                    withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u luis486 -p ${dockerhubpwd}'
                    }
                    sh 'docker push luis486/devops-integration'
                }
            }
        }
        stage('Deploy to k8s'){
            steps{
                kubeconfig(serverUrl: '192.168.58.2') {
                    script{
                        sh 'kubectl apply -f desploymentservice.yaml'
                    }
                }
            }
        }

    }
}