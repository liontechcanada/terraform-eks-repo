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
//         // }
        
//         stage('Terraform Destroy') {
//             when {
//                 expression { params.DESTROY == true }
//             }
//             steps {
//                 timeout(time: 10, unit: 'MINUTES') {
//                     input(message: 'CONFIRM DESTRUCTION?')
//                 }
//                 dir('terraform') {
//                     sh 'terraform destroy -auto-approve'
//                 }
//             }
//         }
//     }
// }



stage('Terraform Destroy') {
    steps {
        dir('terraform') {
            // Show what will be destroyed
            sh 'terraform plan -destroy'
            
            // Require manual confirmation
            timeout(time: 30, unit: 'MINUTES') {
                input(
                    message: 'WARNING: This will DESTROY all infrastructure!', 
                    ok: 'Confirm Destruction',
                    submitterParameter: 'confirm_destroy'
                )
            }
            
            // Execute destruction
            sh 'terraform destroy -auto-approve'
            
            // Clean up state files (optional)
            sh 'rm -f terraform.tfstate* .terraform.lock.hcl'
        }
    }
}