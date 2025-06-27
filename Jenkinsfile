pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    triggers {
        pollSCM('* * * * *')  // Optional: can remove if using GitHub webhooks
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup .NET') {
            steps {
                sh '''
                    DOTNET_VERSION=6.0.417

                    echo "Installing .NET SDK $DOTNET_VERSION..."
                    wget https://dotnet.microsoft.com/download/dotnet/scripts/v1/dotnet-install.sh -O dotnet-install.sh
                    chmod +x dotnet-install.sh
                    ./dotnet-install.sh --version $DOTNET_VERSION --install-dir $HOME/dotnet

                    # Add to PATH
                    export PATH=$HOME/dotnet:$PATH
                    echo "DOTNET_ROOT=$HOME/dotnet" >> $GITHUB_ENV || true
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
                sh 'dotnet test --no-build'
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
        }
    }
}
