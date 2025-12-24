pipeline {
    agent any
 
    stages {
        stage('Checkout') {
            steps {
                // Automatically pulls the code from whichever repo you configured
                checkout scm
            }
        }
 
        stage('Security Gate') {
            parallel {
                // SCAN 1: Semgrep (Code Logic & Secrets)
                stage('Semgrep SAST') {
                    steps {
                        echo '🕵️ Running Deep Code Analysis...'
                        // --error: Forces the build to FAIL if bugs/secrets are found
                        sh 'semgrep scan --config=p/default --config=p/secrets --error .'
                    }
                }
                // SCAN 2: Trivy (Library Vulnerabilities)
                stage('Trivy SCA') {
                    steps {
                        echo '📦 Scanning Dependencies (Libraries)...'
                        // --exit-code 1: Forces the build to FAIL if Critical issues are found
                        sh 'trivy fs --exit-code 1 --severity HIGH,CRITICAL .'
                    }
                }
            }
        }
    }
 
    // This section runs automatically at the end
    post {
        success {
            echo '✅ GREAT JOB! No critical vulnerabilities found.'
        }
        failure {
            echo '❌ SECURITY ALERT! Vulnerabilities detected. Check the logs above.'
        }
    }
}
