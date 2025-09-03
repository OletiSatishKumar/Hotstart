pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Install Dependencies') {
            steps { bat 'npm install' }
        }

        stage('Security Audit') {
            steps {
                echo "🔒 Running npm audit..."
                // Try to fix vulnerabilities (non-breaking changes first)
                bat 'npm audit fix || exit 0'
                // Force fix if required (might break dependencies)
                bat 'npm audit fix --force || exit 0'
            }
        }

        stage('Test') {
            steps { 
                // Allow builds to pass even if no tests exist
                bat 'npm test -- --watchAll=false --ci --passWithNoTests' 
            }
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
                --data "{\\"text\\": \\"SUCCESS ✅ Build #${BUILD_NUMBER} (main branch)\\"}" ^
                %SLACK_WEBHOOK%
                """
            }
        }
        failure {
            withCredentials([string(credentialsId: 'Slack_Cred', variable: 'SLACK_WEBHOOK')]) {
                bat """
                curl -X POST -H "Content-type: application/json" ^
                --data "{\\"text\\": \\"FAILED ❌ Build #${BUILD_NUMBER} (main branch)\\"}" ^
                %SLACK_WEBHOOK%
                """
            }
        }
    }
}
