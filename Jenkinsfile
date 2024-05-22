pipeline {
    agent {
                label 'docker-inbound-agent-dock-cli'
    }

    environment {
        DOCKER_HOST = 'tcp://192.168.1.171:2376' // External Docker host
    }

    stages {
        stage('Start Selenium Grid') {
            steps {
                script {
                    // Pull Docker images
                    // sh 'docker -H $DOCKER_HOST pull selenium/hub:latest'
                    // sh 'docker -H $DOCKER_HOST pull selenium/node-chrome:latest'
                    // sh 'docker -H $DOCKER_HOST pull selenium/node-edge:latest'
                    
                    // Start Docker Compose
                    sh 'docker -H $DOCKER_HOST compose -f compose.yaml up -d'
                    
                    // Get the IP address of the Selenium Hub container
                    def seleniumHubIp = sh(script: 'docker -H $DOCKER_HOST inspect -f "{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}" selenium-hub', returnStdout: true).trim()
                    env.SELENIUM_HUB_IP = seleniumHubIp
                    
                    // Print the IP address of the Selenium Hub container for debugging
                    echo "Selenium Hub IP: ${env.SELENIUM_HUB_IP}"
                    echo "Variable seleniumHubIp is: ${seleniumHubIp}"
                }
            }
        }

        stage('Run Tests') {
            agent {
                label 'docker-jnlp-agent'
            }
            steps {
                script {
                    // Clone the repository
                    git branch: 'main', url: 'https://github.com/watercloset128/Selenium_CI_Demo.git'
                    
                    // Install dependencies
                    sh 'pip install -r requirements.txt'
                    
                    // Run pytest with the Selenium Hub IP
                    sh "pytest --html=reports/latest-automation-report-Edge.html --self-contained-html -n 2 --seleniumhub=${env.SELENIUM_HUB_IP}:4444 ."
                }
            }
            post {
                always {
                    // Archive the report
                    archiveArtifacts artifacts: 'reports/latest-automation-report-Edge.html', allowEmptyArchive: true
                }
            }
        }
    }
    
    post {
        always {
                script {
                    // Stop Docker Compose
                    sh 'docker -H $DOCKER_HOST compose -f compose.yaml down'
                }
        }
    }
}