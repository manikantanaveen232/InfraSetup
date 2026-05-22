pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        TF_IN_AUTOMATION = 'true'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                url: 'https://github.com/manikantanaveen232/InfraSetup.git'
            }
        }

        stage('Clean Terraform Cache') {
            steps {
                sh '''
                    rm -rf .terraform
                    rm -f .terraform.lock.hcl
                '''
            }
        }

        stage('Terraform Init') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-creds']
                ]) {
                    sh '''
                        export AWS_REGION=us-east-1
                        terraform init -upgrade
                    '''
                }
            }
        }

        stage('Terraform Validate') {
            steps {
                sh 'terraform validate'
            }
        }

        stage('Terraform Plan') {
            steps {
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-creds']
                ]) {
                    sh '''
                        export AWS_REGION=us-east-1
                        terraform plan
                    '''
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                input message: "Approve Terraform Apply?"
                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                     credentialsId: 'aws-creds']
                ]) {
                    sh '''
                        export AWS_REGION=us-east-1
                        terraform apply -auto-approve
                    '''
                }
            }
        }

    }
}