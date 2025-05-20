pipeline {
    agent {
        label 'terraform' // Agent with Terraform installed
    }

    environment {
        AWS_ACCESS_KEY_ID     = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        // TF_VAR_environment   = "${env.BRANCH_NAME == 'main' ? 'prod' : 'dev'}"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/liontechcanada/terraform-eks-repo.git'
            }
        }

        stage('Terraform Init') {
            steps {
                dir('terraform') {
                    sh 'terraform init -input=false'
                }
            }
        }

        stage('Terraform Plan') {
            steps {
                dir('terraform') {
                    sh 'terraform plan -out=tfplan -input=false'
                    archiveArtifacts artifacts: 'terraform/tfplan', onlyIfSuccessful: true
                }
            }
        }

        stage('Manual Approval') {
            when {
                branch 'main' // Only require approval for production
            }
            steps {
                timeout(time: 30, unit: 'MINUTES') {
                    input message: 'Apply Terraform changes?', ok: 'Apply'
                }
            }
        }

        stage('Terraform Apply') {
            steps {
                dir('terraform') {
                    sh 'terraform apply -input=false tfplan'
                }
            }
        }

        stage('Post Deployment Tests') {
            steps {
                // Add your integration tests here
                echo "Running post-deployment tests..."
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        failure {
            slackSend channel: '#infra-alerts', message: "Terraform pipeline failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
        }
        success {
            slackSend channel: '#infra-notifications', message: "Terraform pipeline succeeded: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
        }
    }
}