pipeline {
    agent any

    environment {
        AWS_CLI_PATH = "/var/jenkins_home/.local/bin/aws"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git 'https://github.com/kilik42/fuzzy-umbrella'
            }
        }

        stage('Install or Update AWS CLI') {
            steps {
                script {
                    sh '''
                    if [ -x "$AWS_CLI_PATH" ]; then
                        echo "AWS CLI is already installed. Updating..."
                        ./aws/install -i /var/jenkins_home/.local/aws-cli -b /var/jenkins_home/.local/bin --update
                    else
                        echo "AWS CLI not found. Installing..."
                        curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
                        unzip -o awscliv2.zip
                        ./aws/install -i /var/jenkins_home/.local/aws-cli -b /var/jenkins_home/.local/bin
                    fi
                    export PATH=/var/jenkins_home/.local/bin:$PATH
                    '''
                }
            }
        }

        stage('Set AWS Credentials') {
            steps {
                script {
                    withCredentials([aws(credentialsId: 'aws-credentials', accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                        sh '''
                        export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                        export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                        aws configure set region us-east-1
                        '''
                    }
                }
            }
        }

        stage('Initialize Terraform') {
            steps {
                script {
                    sh '''
                    if [ ! -d ".terraform" ]; then
                        terraform init
                    else
                        echo "Terraform already initialized."
                    fi
                    '''
                }
            }
        }

        stage('Plan Terraform') {
            steps {
                script {
                    sh 'terraform plan -out=tfplan'
                }
            }
        }

        stage('Apply Terraform') {
            steps {
                script {
                    sh 'terraform apply -auto-approve tfplan'
                }
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed! Check logs for details."
        }
    }
}
