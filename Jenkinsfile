pipeline {
    agent {label 'agent1'}

    environment {
        DENO_INSTALL = "${HOME}/.deno"
        PATH = "${DENO_INSTALL}/bin:${PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/BrunoF051/fresh-project.git'
            }
        }

        stage('Install Deno') {
            steps {
                sh '''
                    curl -fsSL https://deno.land/x/install/install.sh | sh
                    deno --version
                '''
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
