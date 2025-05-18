pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/jatinder9011/final-project.git', branch: 'main'
            }
        }

        stage('Failing Stage') {
            steps {
                // Force a failure to test Slack notification
                sh 'exit 1'
            }
        }
    }

    post {
        failure {
            script {
                def slackWebhook = 'https://hooks.slack.com/services/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' // 🔁 Replace with real one
                def message = """{i
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
