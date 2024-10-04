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
                    # Install 7zip
                    mkdir -p ${SEVENZIP_DIR}
                    curl -L https://www.7-zip.org/a/7z2201-linux-x64.tar.xz -o ${SEVENZIP_DIR}/7zip.tar.xz
                    tar -xf ${SEVENZIP_DIR}/7zip.tar.xz -C ${SEVENZIP_DIR}
                    chmod +x ${SEVENZIP_DIR}/7zz
                    export PATH="${SEVENZIP_DIR}:${PATH}"

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
