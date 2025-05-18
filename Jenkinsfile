pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/jatinder9011/final-project.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t html-webapp:secure .'
            }
        }

        stage('Trivy Scan') {
            steps {
                script {
                    def result = sh(script: "trivy image --severity HIGH,CRITICAL --exit-code 1 html-webapp:secure", returnStatus: true)
                    if (result != 0) {
                        error "Trivy scan failed due to HIGH or CRITICAL vulnerabilities"
                    }
                }
            }
        }

        // Optional: Simulate failure to test Slack alert
        stage('Force Fail for Testing Slack') {
            steps {
                sh 'exit 1'
            }
        }
    }

    post {
        failure {
            withCredentials([string(credentialsId: 'SLACK_WEBHOOK', variable: 'SLACK_URL')]) {
                script {
                    def message = """{
                      "text": "❌ Jenkins build *failed* for job: ${env.JOB_NAME} build #${env.BUILD_NUMBER}. Check: ${env.BUILD_URL}"
                    }"""
                    sh """
                        curl -X POST -H 'Content-type: application/json' \
                        --data '${message}' $SLACK_URL
                    """
                }
            }
        }
    }
}
