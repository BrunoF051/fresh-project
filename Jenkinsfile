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

        stage('Format Check') {
            steps {
                sh 'deno fmt --check'
            }
        }

        stage('Test') {
            steps {
                sh 'deno test'
            }
        }

        stage('Build') {
            steps {
                sh 'deno task build'
            }
        }

        stage('Deploy') {
            when {
                branch 'master'
            }
            steps {
                // Add your deployment steps here
                echo 'Deploying...'
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
