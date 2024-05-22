pipeline {
    agent none
    stages {
        stage('Init') {
            agent {
                label 'docker-inbound-agent-dock-cli'
            }
            steps {
                sh 'docker --version'
                sh 'docker run hello-world'
                sh 'docker ps'
            }
        }
    }
}