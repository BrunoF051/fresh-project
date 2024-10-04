pipeline {
    agent { label 'agent1' }

    environment {
        DENO_INSTALL = "${HOME}/.deno"
        PATH = "${DENO_INSTALL}/bin:${PATH}"
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
                    echo 'export DENO_INSTALL="$HOME/.deno"' >> $HOME/.bashrc
                    echo 'export PATH="$DENO_INSTALL/bin:$PATH"' >> $HOME/.bashrc
                    source $HOME/.bashrc
                '''
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
