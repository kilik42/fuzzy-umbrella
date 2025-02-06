pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        AWS_CLI_PATH = "/var/jenkins_home/.local/bin"
    }

    stages {
        stage('Setup Environment') {
            steps {
                script {
                    sh '''
                    echo "🔧 Setting up environment variables..."
                    export PATH=$AWS_CLI_PATH:$PATH
                    echo 'export PATH=$AWS_CLI_PATH:$PATH' >> ~/.bashrc
                    echo 'export PATH=$AWS_CLI_PATH:$PATH' >> ~/.profile
                    '''
                }
            }
        }

        stage('Install/Update AWS CLI') {
            steps {
                script {
                    sh '''
                    echo "🔍 Checking if AWS CLI is installed..."
                    if ! command -v aws &> /dev/null
                    then
                        echo "⚙️ AWS CLI not found. Installing..."
                        curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
                        unzip -o awscliv2.zip
                        ./aws/install -i /var/jenkins_home/.local/aws-cli -b /var/jenkins_home/.local/bin
                        echo "✅ AWS CLI Installed Successfully!"
                    else
                        echo "🔄 AWS CLI already installed. Updating..."
                        ./aws/install --update -i /var/jenkins_home/.local/aws-cli -b /var/jenkins_home/.local/bin
                        echo "✅ AWS CLI Updated Successfully!"
                    fi

                    echo "🔄 Verifying AWS Installation..."
                    ls -l /var/jenkins_home/.local/bin/aws
                    which aws
                    aws --version
                    '''
                }
            }
        }

        stage('Set AWS Credentials') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh '''
                    echo "🔑 Configuring AWS Credentials..."
                    export PATH=$AWS_CLI_PATH:$PATH
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
                echo "🚀 Initializing Terraform..."
                terraform init
                '''
            }
        }

        stage('Plan Terraform') {
            steps {
                sh '''
                echo "📋 Running Terraform Plan..."
                terraform plan
                '''
            }
        }

        stage('Apply Terraform') {
            steps {
                sh '''
                echo "⚡ Applying Terraform Configuration..."
                terraform apply -auto-approve
                '''
            }
        }
    }

    post {
        always {
            echo "✅ Terraform deployment finished!"
        }
        success {
            echo "🎉 Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
