pipeline {
    agent any
    tools {
        maven "Maven"
    }
    environment {
        my_k8s_ecr_repo = '358966077154.dkr.ecr.us-east-1.amazonaws.com/eks-helm-may25'
    }

    stages {
        stage('Git Checkout') {
            steps {
                    checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/WillKay28/EKS-K8S-Cluster-Helm-Jenkins-Springboot-MicrosvcsApp']])
            }
        }
        stage('Build Jar') {
            steps {
                sh 'mvn clean install'
            }
        }
        stage('Build Image & Tag') {
            steps {
                script {
                    dockerImage = docker.build my_k8s_ecr_repo
                    dockerImage.tag("$BUILD_NUMBER")
                }
            }
        }
        stage('Push Image to ECR') {
            steps {
                script {
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 358966077154.dkr.ecr.us-east-1.amazonaws.com'
                    sh 'docker push 358966077154.dkr.ecr.us-east-1.amazonaws.com/eks-helm-may25:$BUILD_NUMBER'
                }
            }
        }
        stage('Helm Deploy') {
            steps {
                script {
                    sh 'helm upgrade --install my_eks_k8s_cluster_charts --namespace my-eks-helm-deployment --set image.tag=$BUILD_NUMBER'
                }
            }
        }
    }
}
