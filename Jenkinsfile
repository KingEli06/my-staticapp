pipeline{
    agent any
    stages{
        stage('CodeScan'){
            steps{
                sh 'trivy fs . -o file.txt'
                sh 'cat file.txt'

            }
        }
        stage('build'){
            steps{
                sh 'ls'
                sh 'touch eric.txt'

            }
        }
    }
}