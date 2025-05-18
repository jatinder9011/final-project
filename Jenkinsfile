pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQube-Server'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/jatinder9011/final-project.git', branch: 'main'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    script {
                        def scannerHome = tool 'SonarScanner'
                        sh "${scannerHome}/bin/sonar-scanner"
                    }
                }
            }
        }

      //  stage('Quality Gate') {
      //    steps {
      //          timeout(time: 5, unit: 'MINUTES') {
      //              waitForQualityGate abortPipeline: true
      //          }
       //     }
      //  }
                stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("html-webapp:secure")
                }
            }
        }

        stage('Scan with Trivy') {
            steps {
                script {
                    def result = sh(script: "trivy image --severity HIGH,CRITICAL --exit-code 1 html-webapp:secure", returnStatus: true)
                    if (result != 0) {
                        error "Trivy scan failed due to HIGH or CRITICAL vulnerabilities"
                    }
                }
            }
        }
    }
    // End of stages

    post {
        failure {
            script {
                def slackWebhook = 'https://hooks.slack.com/services/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'
                def message = """{
                  "text": "❌ Jenkins build *failed* for job: ${env.JOB_NAME} build #${env.BUILD_NUMBER}. Check: ${env.BUILD_URL}"
                }"""
                sh """
                    curl -X POST -H 'Content-type: application/json' \
                    --data '${message}' ${slackWebhook}
                """
            }
        }
    }
} 
