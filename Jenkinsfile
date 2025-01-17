pipeline {
    agent any
    environment{
        AWS_ACCESS_KEY_ID = credentials('AWS_ACCESS_KEY_ID')
        AWS_SECRET_ACCESS_KEY = credentials('AWS_SECRET_ACCESS_KEY')
        AWS_DEFAULT_REGION = "us-east-1"

    }

    stages {
        stage('Checkout SCM') {
            steps {
                echo 'Git checkout'
                script{
                   checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/4ennyx/Terraform-jenkins-EKS-Project-.git']])
                }
                
            }
        }
        stage('Intitializing Terraform'){
            steps{
               script{
                 dir('EKS-Cluster') {
                    sh 'terraform init'
                 }
               }
            }
        }
        stage('Formatting Terraform'){
            steps{
               script{
                 dir('EKS-Cluster') {
                    sh 'terraform fmt'
                 }
               }
            }
        }
        stage('Validate Terraform'){
            steps{
               script{
                 dir('EKS-Cluster') {
                    sh 'terraform validate'
                 }
               }
            }
        }
        stage('Previewing the Terraform infra'){
            steps{
               script{
                 dir('EKS-Cluster') {
                    sh 'terraform plan'
                 }
                 input(message: "Are you sure to proceed"?, ok:"Proceed")
               }
            }
        }
        stage('Creating/Destroying an EKS Cluster'){
            steps{
               script{
                 dir('EKS-Cluster') {
                    sh 'terraform $action --auto-approve'
                 }
               }
            }
        }
        stage("Deploy nginx Application"){
            steps{
                script{
                    dir('EKS-Cluster/ConfigurationFiles'){
                        sh 'aws eks update-kubeconfig --name my-eks-cluster'
                        sh 'kubectl apply -f Deployment.yaml'
                        sh 'kubectl apply -f service.yaml'
                    }
                }
            }
        }
    }
}