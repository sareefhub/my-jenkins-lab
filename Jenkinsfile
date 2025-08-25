pipeline {
    agent any

    parameters {
        booleanParam(name: 'RUN_DEPLOY', defaultValue: true, description: 'Should we deploy?')
        choice(name: 'ENV', choices: ['dev', 'staging', 'prod'], description: 'Select deployment environment')
    }

    stages {
        stage('Build') {
            steps {
                echo '🔨 Building application...'
                sh 'ls -l'   // แสดงไฟล์ใน workspace
            }
        }

        stage('Test in Parallel') {
            parallel {
                stage('Unit Tests') {
                    steps {
                        echo '🧪 Running unit tests...'
                        sh 'echo "Unit tests passed!" > unit_results.txt'
                    }
                }
                stage('Integration Tests') {
                    steps {
                        echo '🔗 Running integration tests...'
                        sh 'echo "Integration tests passed!" > integration_results.txt'
                    }
                }
            }
        }

        stage('Archive Test Results') {
            steps {
                sh 'echo "All tests completed!" > results.txt'
                archiveArtifacts artifacts: '*.txt', fingerprint: true
            }
        }

        stage('Approval') {
            options {
                timeout(time: 2, unit: 'MINUTES')  // ถ้าไม่ approve ภายใน 2 นาที จะ fail
            }
            steps {
                input "Do you want to proceed with deployment?"
            }
        }

        stage('Deploy') {
            when {
                expression { return params.RUN_DEPLOY }
            }
            steps {
                echo "🚀 Deploying application to ${params.ENV} environment..."
                sh 'echo "Deployment completed!"'
            }
        }

        stage('Cross Platform Tests (Simulated)') {
            parallel {
                stage('Linux') {
                    steps { echo '🐧 Running tests on Linux...' }
                }
                stage('Windows') {
                    steps { echo '🪟 Running tests on Windows...' }
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline finished successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs!'
        }
        always {
            echo '📌 Pipeline completed (success or failure).'
        }
    }
}
