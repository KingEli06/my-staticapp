/*

pipeline{
    agent any
    stages{
        stage('CodeScan'){
            steps{
                sh 'trivy fs . -o file.txt'
                sh 'cat file.txt'
            }
        }
        stage('DockerLogin'){
            steps{
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 529088290671.dkr.ecr.us-east-1.amazonaws.com'

            }
        }
        stage('DockerBuild'){
            steps{
                sh 'docker build -t group-project .'
                sh 'docker build -t newversion .'
            }
        }
        stage('DockerImageTag'){
            steps{
                sh 'docker tag group-project:latest 529088290671.dkr.ecr.us-east-1.amazonaws.com/group-project:latest'
                sh 'docker tag group-project:latest 529088290671.dkr.ecr.us-east-1.amazonaws.com/group-project:v1.$BUILD_NUMBER'
            }
        }
        stage('DockerPushImage'){
            steps{
                sh 'docker push 529088290671.dkr.ecr.us-east-1.amazonaws.com/group-project:latest'
                sh 'docker push 
            }
        }
        
    }
}
*/


pipeline {
    agent any
    environment {
        ECR_REPO = "529088290671.dkr.ecr.us-east-1.amazonaws.com/group-project"
        AWS_REGION = "us-east-1"
    }
    stages {
        stage('CodeScan') {
            steps {
                sh 'trivy fs . -o file.txt'
                sh 'cat file.txt'
            }
        }
        stage('DockerLogin') {
            steps {
                sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO'
            }
        }
        stage('Retrieve Previous Version') {
            steps {
                script {
                    def previousTag = sh(script: "aws ecr list-images --repository-name group-project --query 'sort_by(imageDetails,& imagePushedAt)[-1].imageTags[0]' --output text", returnStdout: true).trim()
                    env.PREVIOUS_VERSION = previousTag ?: "none"
                    echo "Previous Version: ${env.PREVIOUS_VERSION}"
                }
            }
        }
        stage('DockerBuild') {
            steps {
                sh 'docker build -t group-project .'
                sh 'docker tag group-project:latest newversion'
            }
        }
        stage('DockerImageTag') {
            steps {
                script {
                    def newTag = "v1.${BUILD_NUMBER}"
                    env.NEW_VERSION = newTag

                    // Tag the new build
                    sh "docker tag group-project:latest $ECR_REPO:latest"
                    sh "docker tag group-project:latest $ECR_REPO:$newTag"

                    // Keep previous version tagged
                    if (env.PREVIOUS_VERSION != "none") {
                        sh "docker tag $ECR_REPO:latest $ECR_REPO:previous"
                    }
                }
            }
        }
        stage('DockerPushImage') {
            steps {
                sh "docker push $ECR_REPO:latest"
                sh "docker push $ECR_REPO:$NEW_VERSION"

                // Push the backup previous version
                script {
                    if (env.PREVIOUS_VERSION != "none") {
                        sh "docker push $ECR_REPO:previous"
                    }
                }
            }
        }
    }
}
