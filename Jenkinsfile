pipeline {
    agent {
        docker {
            image 'denoland/deno:2.0.0-rc.10'
            args '--network host'
        }
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Cache Dependencies') {
            steps {
                sh 'deno cache main.ts'
            }
        }

        stage('Lint') {
            steps {
                sh 'deno lint'
            }
        }

        stage('Format') {
            steps {
                sh 'deno fmt'
                sh 'git diff'  // This will show what changed
                sh 'git add .'
                sh 'git commit -m "Auto-format code" || true'
            }
        }

        stage('Test') {
            steps {
                script {
                    def testFiles = sh(script: 'find . -name "*_test.ts" -o -name "*.test.ts"', returnStdout: true).trim()
                    if (testFiles) {
                        sh "deno test ${testFiles}"
                    } else {
                        echo "No test files found. Skipping tests."
                    }
                }
            }
        }

        stage('Build') {
            steps {
                sh 'deno task build'
            }
        }

        stage('Run and Preview') {
            steps {
                script {
                    // Start the application in the background
                    sh 'deno run --allow-net main.ts &'

                    // Wait for the application to start
                    sh 'sleep 5'

                    // Check if the application is running
                    sh 'curl -f http://localhost:8000 || (echo "Application failed to start" && exit 1)'

                    echo 'Application is running. You can access it at http://localhost:8000'

                    // Wait for user input to stop the application
                    input message: 'Application is running. Click "Proceed" to stop the application and continue.'
                }
            }
            post {
                always {
                    sh 'deno --unstable kill || true'
                    echo 'Preview server stopped.'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
