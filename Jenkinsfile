pipeline {
    agent any

    environment {
        AWS_DEFAULT_REGION = 'us-east-1'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/manikantanaveen232/InfraSetup.git'
            }
        }

        stage('Terraform Deploy') {
            options {
                timeout(time: 60, unit: 'MINUTES')
            }

            steps {

                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds']
                ]) {

                    sh '''
                    terraform init
                    terraform validate
                    terraform plan -out=tfplan
                    '''

                    input message: 'Approve Terraform Apply?'

                    sh '''
                    terraform apply -auto-approve tfplan
                    '''
                }
            }
        }

        stage('Configure kubectl') {
            steps {

                withCredentials([
                    [$class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds']
                ]) {

                    sh '''
                    aws eks update-kubeconfig \
                    --name eks-prod \
                    --region us-east-1
                    '''
                }
            }
        }

        stage('Verify Cluster') {
            steps {

                sh '''
                kubectl get nodes
                kubectl get pods -A
                '''
            }
        }
    }

    post {

        success {
            echo 'EKS Cluster Created Successfully'
        }

        failure {
            echo 'Pipeline Failed'
        }

        always {
            cleanWs()
        }
    }
}