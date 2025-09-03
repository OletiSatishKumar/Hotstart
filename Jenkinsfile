pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Install Dependencies') {
            steps { bat 'npm install' }
        }

        stage('Lint') {
            steps { bat 'npm run lint' }
        }

        stage('Test') {
            steps { bat 'npm test -- --watchAll=false --ci' }
        }

        stage('Build') {
            steps { bat 'npm run build' }
        }

        stage('Deploy') {
            when { branch 'main' }
            steps {
                echo "🚀 Deploying production build from MAIN branch..."
                // Insert Docker build/push or Kubernetes deploy here
            }
        }
    }

    post {
        success {
            withCredentials([string(credentialsId: 'Slack_Cred', variable: 'SLACK_WEBHOOK')]) {
                bat """
                curl -X POST -H "Content-type: application/json" ^
                --data "{\\"text\\": \\"✅ SUCCESS: Build #${BUILD_NUMBER} for branch ${BRANCH_NAME}\\"}" ^
                %SLACK_WEBHOOK%
                """
            }
        }
        failure {
            withCredentials([string(credentialsId: 'Slack_Cred', variable: 'SLACK_WEBHOOK')]) {
                bat """
                curl -X POST -H "Content-type: application/json" ^
                --data "{\\"text\\": \\"❌ FAILED: Build #${BUILD_NUMBER} for branch ${BRANCH_NAME}\\"}" ^
                %SLACK_WEBHOOK%
                """
            }
        }
    }
}
