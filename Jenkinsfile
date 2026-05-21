pipeline {
    agent any

    environment {
        AWS_ACCESS_KEY_ID     = credentials('aws-jenkins-creds')
        AWS_SECRET_ACCESS_KEY = credentials('aws-jenkins-creds')
        AWS_DEFAULT_REGION    = 'us-east-1'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git 'https://github.com/your-repo/terraform-eks.git'
            }
        }

        stage('Terraform Init') {
            steps {
                sh 'terraform init'
            }
        }

        stage('Terraform Validate') {
            steps {
                sh 'terraform validate'
            }
        }

        stage('Terraform Plan') {
            steps {
                sh 'terraform plan'
            }
        }

        stage('Terraform Apply') {
            steps {
                sh 'terraform apply -auto-approve'
            }
        }

        stage('Configure kubectl') {
            steps {
                sh '''
                aws eks update-kubeconfig \
                  --region ap-south-1 \
                  --name jenkins-eks
                '''
            }
        }

        stage('Verify Cluster') {
            steps {
                sh 'kubectl get nodes'
            }
        }
    }
}
