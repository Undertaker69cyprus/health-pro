pipeline {
    agent any
    
    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/Undertaker69cyprus/health-pro/', branch: 'master'
                echo 'GitHub repository checked out'
            }
        }
        
        stage('Build and Test') {
            steps {
                echo 'Starting compilation and tests'
                sh 'mvn clean compile test'
            }
        }
        
        stage('Quality Analysis') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }
        
        stage('Package') {
            steps {
                sh 'mvn package install'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t addi2/endtoendproject:v1 .'
                }
            }
        }
        
        stage('Docker Login and Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-pwd', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh 'docker push addi2/endtoendproject:v1'
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            when { 
                expression { env.GIT_BRANCH == 'master' }
            }
            steps {
                script {
                    kubernetesDeploy(configs: 'deploymentservice.yaml', kubeconfigId: 'k8sconfigpwd')
                }
            }
        }
    }
}
