pipeline {
    agent { label 'agent1' }

    environment {
        DENO_INSTALL = "${HOME}/.deno"
        PATH = "${env.DENO_INSTALL}/bin:${env.PATH}"
    }

    stages {
        stage('Setup') {
            steps {
                sh '''
                    # Install unzip (required for Deno installation)
                    apt-get update
                    apt-get install -y unzip

                    # Install Deno
                    curl -fsSL https://deno.land/x/install/install.sh | sh

                    # Update PATH for this session
                    export DENO_INSTALL="${HOME}/.deno"
                    export PATH="${DENO_INSTALL}/bin:${PATH}"

                    # Verify Deno installation
                    deno --version
                '''

                // Update Jenkins environment variables
                script {
                    env.DENO_INSTALL = "${HOME}/.deno"
                    env.PATH = "${env.DENO_INSTALL}/bin:${env.PATH}"
                }
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install dependencies') {
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
                sh 'deno fmt --check'
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

        stage('Preview') {
            steps {
                script {
                    // Start the preview in the background
                    sh 'nohup deno task preview > preview.log 2>&1 &'

                    // Wait for the server to start
                    sh 'sleep 5'

                    // Check if the server is running
                    sh 'curl -f http://localhost:8000 || (echo "Preview server failed to start" && exit 1)'

                    echo 'Preview server is running. You can access it at http://localhost:8000'

                    // Wait for user input to stop the preview
                    input message: 'Preview is running. Click "Proceed" to stop the preview and continue.'
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
