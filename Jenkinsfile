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
                    # Update and install necessary tools
                    apt-get update
                    apt-get install -y curl git unzip

                    # Install Deno
                    curl -fsSL https://deno.land/x/install/install.sh | sh
                    echo 'export DENO_INSTALL="/home/jenkins/.deno"' >> $HOME/.bashrc
                    echo 'export PATH="$DENO_INSTALL/bin:$PATH"' >> $HOME/.bashrc
                    source $HOME/.bashrc
                    deno --version
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
