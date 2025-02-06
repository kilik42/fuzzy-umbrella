pipeline {
    agent any
    environment {
        AWS_REGION = 'us-east-1' 
    }
    stages {
        stage('Install AWS CLI') {
            steps {
                sh '''
                if ! command -v aws &> /dev/null
                then
                    echo "AWS CLI not found. Installing..."
                    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
                    unzip awscliv2.zip
                    ./aws/install
                fi
                aws --version
                '''
            }
        }
        stage('Set AWS Credentials') {
            steps {
                withCredentials([aws(accessKeyVariable: 'AWS_ACCESS_KEY_ID', secretKeyVariable: 'AWS_SECRET_ACCESS_KEY')]) {
                    sh 'aws sts get-caller-identity'
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
                sh 'terraform init'
            }
        }
        stage('Plan Terraform') {
            steps {
                sh 'terraform plan'
            }
        }
        stage('Apply Terraform') {
            steps {
                sh 'terraform apply -auto-approve'
            }
        }
    } // Closing `stages`

    post {
        always {
            echo "Terraform deployment finished!"
        }
        success {
            echo "Pipeline completed successfully!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
} // Closing `pipeline`
