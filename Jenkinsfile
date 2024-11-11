pipeline {
    agent { label 'slave' }
    
    environment {
        DOCKERHUB_CREDENTIALS=credentials ('dockerhub')
    }

    stages {
        stage('SCM_Checkout') {
            steps {
                echo 'Clone the github repo'
                git 'https://github.com/deepikaarjunan22/star-agile-banking-finance.git'
            }
        }
        stage ('Build') {
            steps {
                echo 'build using maven'
                sh 'mvn clean package'
            }
        }
        stage ('docker image') {
            steps {
                echo 'build using docker image'
                sh 'docker build -t deepikaarjunan/finance-me:${BUILD_NUMBER} .'
                sh 'docker tag deepikaarjunan/finance-me:${BUILD_NUMBER} deepikaarjunan/finance-me:latest'
                sh 'docker image list'
            }
        }
        stage ('login') {
            steps {
                echo 'docker login'
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage ('push') {
            steps {
                echo 'docker push'
                sh 'docker push deepikaarjunan/finance-me:latest'
            }
        }
        stage ('deploy using ansible') {
            steps {
                echo 'deploy using ansible'
                ansiblePlaybook become: true, 
                credentialsId: 'ansible-test', 
                disableHostKeyChecking: true, 
                installation: 'ansible', 
                inventory: '/etc/ansible/hosts', 
                playbook: 'ansible-playbook.yml', 
                vaultTmpPath: ''
            }
        }
    }
}
