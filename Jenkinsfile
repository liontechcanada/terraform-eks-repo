pipeline {
    agent any
    
    parameters {
        booleanParam(
            name: 'DESTROY',
            defaultValue: false,
            description: 'Check to DESTROY infrastructure'
        )
    }
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/liontechcanada/terraform-eks-repo.git'
            }
        }
        
        // stage('Terraform Init') {
        //     steps {
        //         dir('terraform') {
        //             sh 'terraform init -input=false'
        //         }
        //     }
        // }
        
        // stage('Terraform Plan') {
        //     when {
        //         expression { params.DESTROY == false }
        //     }
        //     steps {
        //         dir('terraform') {
        //             sh 'terraform plan -out=tfplan'
        //             // archiveArtifacts 'tfplan'
        //         }
        //     }
        // }
        
        // stage('Terraform Apply') {
        //     when {
        //         expression { params.DESTROY == false }
        //     }
        //     steps {
        //         timeout(time: 10, unit: 'MINUTES') {
        //             input(message: 'Apply changes?')
        //         }
        //         dir('terraform') {
        //             sh 'terraform apply -input=false tfplan'
        //         }
        //     }
        // }
        
        stage('Terraform Destroy') {
            when {
                expression { params.DESTROY == true }
            }
            steps {
                timeout(time: 10, unit: 'MINUTES') {
                    input(message: 'CONFIRM DESTRUCTION?')
                }
                dir('terraform') {
                    sh 'terraform destroy -auto-approve'
                }
            }
        }
    }
}