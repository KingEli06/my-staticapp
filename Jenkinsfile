
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
                sh 'docker push 529088290671.dkr.ecr.us-east-1.amazonaws.com/group-project:v1.$BUILD_NUMBER'
            }
        }
        stage('Testing'){
            steps{
                sh 'docker images'
                sh 'docker run -itd --name web2 -p 80:80 group-project'
                sh 'docker ps'
            }
        }
        
    }
}

