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
                    sh 'which aws || curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg" && sudo installer -pkg AWSCLIV2.pkg -target /'
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
