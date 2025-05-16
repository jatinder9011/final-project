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
  }
            
