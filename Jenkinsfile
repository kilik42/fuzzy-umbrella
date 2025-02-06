pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_CLI_PATH = "$HOME/.local/bin"
    }

    stages {
        stage('Setup Environment') {
            steps {
                script {
                    sh '''
                    echo "Setting up environment variables..."
                    export PATH=$AWS_CLI_PATH:$PATH
                    echo 'export PATH=$AWS_CLI_PATH:$PATH' >> ~/.bashrc
                    . ~/.bashrc  # Fix: Use . instead of source
                    '''
                }
            }
        }

        stage('Install AWS CLI') {
            steps {
                script {
                    sh '''
                    if ! command -v aws &> /dev/null
                    then
                        echo "AWS CLI not found. Installing in user space..."
                        curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
                        unzip -o awscliv2.zip  # ✅ Fix: Force overwrite without prompt
                        ./aws/install -i $HOME/.local/aws-cli -b $HOME/.local/bin
                    fi
                    aws --version
                    '''
                }
            }
        }

        stage('Set AWS Credentials') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                    echo "Checking AWS Credentials..."
                    aws sts get-caller-identity
                    '''
                }
            }
        }

        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/kilik42/fuzzy-umbrella', branch: 'main'
            }
        }

        stage('Initialize Terraform') {
            steps {
                sh '''
                echo "Initializing Terraform..."
                terraform init
                '''
            }
        }

        stage('Plan Terraform') {
            steps {
                sh '''
                echo "Running Terraform Plan..."
                terraform plan
                '''
            }
        }

        stage('Apply Terraform') {
            steps {
                sh '''
                echo "Applying Terraform Configuration..."
                terraform apply -auto-approve
                '''
            }
        }
    }

    post {
        always {
            echo "Terraform deployment finished!"
        }
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
