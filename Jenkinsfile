pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    environment {
        DOTNET_VERSION = "6.0.417"
        DOTNET_INSTALL_DIR = "${HOME}/dotnet"
        DOTNET_ROOT = "${HOME}/dotnet"
        PATH = "${HOME}/dotnet:${PATH}"
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage('Branch Filter') {
            when {
                not {
                    anyOf {
                        branch 'main'
                        branch 'develop'
                    }
                }
            }
            steps {
                echo "Current branch: ${env.BRANCH_NAME}"
                echo "Skipping pipeline: this branch is not 'main' or 'develop'."
                script {
                    currentBuild.result = 'SUCCESS'
                    error("Pipeline stopped early for non-target branch.")
                }
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup .NET') {
            steps {
                sh '''
                    echo "Installing .NET SDK $DOTNET_VERSION..."
                    wget https://dotnet.microsoft.com/download/dotnet/scripts/v1/dotnet-install.sh -O dotnet-install.sh
                    chmod +x dotnet-install.sh
                    ./dotnet-install.sh --version $DOTNET_VERSION --install-dir $DOTNET_INSTALL_DIR
                    dotnet --info
                '''
            }
        }

        stage('Restore Dependencies') {
            steps {
                sh 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                sh 'dotnet build --no-restore'
            }
        }

        stage('Test') {
            steps {
                sh 'dotnet test --no-build --framework net6.0'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
        }
    }
}
