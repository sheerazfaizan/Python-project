pipeline {
    agent {label 'KOPS'}

    environment {
       registryCredential = 'ecr:us-east-1:awscreds'
       appRegistry        = "172166620596.dkr.ecr.us-east-1.amazonaws.com/pythonecr"
       pythonRegistry   = "https://172166620596.dkr.ecr.us-east-1.amazonaws.com"
    }
    stages {
        stage('DEV'){
            steps {
                git branch: 'main', url: 'https://github.com/sheerazfaizan/Python-project.git   '
           
                script {
                    dockerImage = docker.build(appRegistry + ":$BUILD_NUMBER", "./")
                }
            
            
                script {
                    docker.withRegistry( pythonRegistry, registryCredential ) {
                        dockerImage.push("$BUILD_NUMBER") 
                        dockerImage.push('latest')
                    }
                }
          
                // Update Kubernetes manifest to use ECR image
                script {
                    def manifestPath = "./kubernetes/deploy.yaml"
                    def ecrImage = "${BUILD_NUMBER}"
                    
                    // Replace image in Kubernetes manifest
                    sh "sed -i 's#<IMAGE_NAME>#${ecrImage}#' ${manifestPath}"
                    
                    // Apply updated manifest to Kubernetes cluster
                    sh "kubectl create namespace dev"
                    sh "kubectl apply -f ./kubernetes/deploy.yaml -n dev"
                    sh "kubectl apply -f ./kubernetes/service.yaml -n dev"
                   
                }
            
        }

    //     stage('QA'){
    //         steps {
    //             git branch: 'main', credentialsId: 'gitsshkey', url: 'git@github.com:QuadriDevOps1/ci-cd-with-kube.git'
    //         }
    //         steps {
    //             script {
    //                 dockerImage = docker.build(appRegistry + ":$BUILD_NUMBER", "./")
    //             }
    //         }
    //         steps{
    //             script {
    //                 docker.withRegistry( pythonRegistry, registryCredential ) {
    //                     dockerImage.push("$BUILD_NUMBER") 
    //                     dockerImage.push('latest')
    //                 }
    //             }
    //         }
    //         steps {
    //             // Update Kubernetes manifest to use ECR image
    //             script {
    //                 def manifestPath = "./kubernetes/app/appdep.yml"
    //                 def ecrImage = "${BUILD_NUMBER}"
                    
    //                 // Replace image in Kubernetes manifest
    //                 sh "sed -i 's#<IMAGE_NAME>#${ecrImage}#' ${manifestPath}"
                    
    //                 // Apply updated manifest to Kubernetes cluster
    //                 sh "kubectl create namespace prod"
    //                 sh "kubectl apply -f ${manifestPath} -n prod"
                   
    //             }
    //         }
    //     }

    //     stage('PROD'){
    //         steps {
    //             git branch: 'main', credentialsId: 'gitsshkey', url: 'git@github.com:QuadriDevOps1/ci-cd-with-kube.git'
    //         }
    //         steps {
    //             script {
    //                 dockerImage = docker.build(appRegistry + ":$BUILD_NUMBER", "./")
    //             }
    //         }
    //         steps{
    //             script {
    //                 docker.withRegistry( pythonRegistry, registryCredential ) {
    //                     dockerImage.push("$BUILD_NUMBER") 
    //                     dockerImage.push('latest')
    //                 }
    //             }
    //         }
    //         steps {
    //             // Update Kubernetes manifest to use ECR image
    //             script {
    //                 def manifestPath = "./kubernetes/app/appdep.yml"
    //                 def ecrImage = "${BUILD_NUMBER}"
                    
    //                 // Replace image in Kubernetes manifest
    //                 sh "sed -i 's#<IMAGE_NAME>#${ecrImage}#' ${manifestPath}"
                    
    //                 // Apply updated manifest to Kubernetes cluster
    //                 sh "kubectl create namespace prod"
    //                 sh "kubectl apply -f ${manifestPath} -n prod"
                   
    //             }
    //         }
    //     }
     }

    }
}
        

        

        
      

        

    
