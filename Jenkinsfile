pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQube-Server'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/vishalkuppusamy/sonarqubecheck.git', branch: 'main'
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

        stage('Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
