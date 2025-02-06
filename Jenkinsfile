pipeline {
    agent any

    environment {
        AWS_CREDENTIALS_ID = 'aws-credentials' // Replace with Jenkins credentials ID
        TF_WORKING_DIR = 'terraform/' // Adjust if necessary
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    try {
                        checkout scm
                    } catch (Exception e) {
                        error "Checkout failed: ${e.message}"
                    }
                }
            }
        }

        stage('Install AWS CLI') {
    steps {
        script {
            sh '''
            if ! command -v aws &> /dev/null; then
                echo "AWS CLI not found. Installing..."
                curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
                unzip awscliv2.zip
                ./aws/install -i /var/jenkins_home/.local/aws-cli -b /var/jenkins_home/.local/bin
                export PATH=/var/jenkins_home/.local/bin:$PATH
            else
                echo "AWS CLI is already installed."
            fi
            '''
        }
    }
}
        stage('Set AWS Credentials') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: env.AWS_CREDENTIALS_ID]]) {
                    sh '''
                        export AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID
                        export AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY
                    '''
                }
            }
        }

        stage('Initialize Terraform') {
            steps {
                script {
                    dir(env.TF_WORKING_DIR) {
                        sh 'terraform init'
                    }
                }
            }
        }

        stage('Plan Terraform') {
            steps {
                script {
                    dir(env.TF_WORKING_DIR) {
                        sh 'terraform plan -out=tfplan'
                    }
                }
            }
        }

        stage('Apply Terraform') {
            steps {
                script {
                    dir(env.TF_WORKING_DIR) {
                        sh 'terraform apply -auto-approve tfplan'
                    }
                }
            }
        }
    }

    post {
        success {
            echo '✅ Terraform deployment finished successfully!'
        }
        failure {
            echo '❌ Pipeline failed! Check logs for details.'
        }
    }
}
