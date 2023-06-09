pipeline {
    parameters {
    string(name: 'ENVIRONMENT', defaultValue: 'dev', description: 'Environment to deploy to (dev, qa, or prod)')
    }
    agent any

    environment {
       registryCredential = 'ecr:us-east-1:awscreds'
       appRegistry        = "172166620596.dkr.ecr.us-east-1.amazonaws.com/pythonecr"
       pythonRegistry     = "https://172166620596.dkr.ecr.us-east-1.amazonaws.com"
    }
    stages {
        stage('GitCheckout') {
            steps {
                git branch: 'main', url: 'https://github.com/sheerazfaizan/Python-project.git'
            }
        }
        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build("${appRegistry}:${BUILD_NUMBER}", "./")
                }
            }
        }

        stage('Push Docker Image to ECR') {
            steps {   
                script {
                    docker.withRegistry( pythonRegistry, registryCredential ) {
                        dockerImage.push("$BUILD_NUMBER") 
                        dockerImage.push('latest')
                    }
                }
            }
         }

        stage('Deploy to KOPS Cluster') {
            agent {label 'KOPS'}
            steps {         
                // Update Kubernetes manifest to use ECR image
                script {
                    def manifestPath = "./kubernetes/deploy.yaml"
                    def ecrImage = "${appRegistry}:${BUILD_NUMBER}"
                    sh "echo $ecrImage"
                    
                    // Replace image in Kubernetes manifest
                    sh "sed -i 's|IMAGE_NAME|$ecrImage|g' ./kubernetes/deploy.yaml"
                    
                    // Apply updated manifest to Kubernetes cluster
                    sh "kubectl create namespace $ENVIRONMENT"
                    sh "kubectl apply -f ./kubernetes/deploy.yaml -n $ENVIRONMENT"
                    sh "kubectl apply -f ./kubernetes/service.yaml -n $ENVIRONMENT"
                
                }
            }
            
        }
     }

}

        

        

        
      

        

    
