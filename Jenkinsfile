pipeline{
    agent any
    stages{
        stage('CodeScan'){
            steps{
                sh 'trivy fs . -o file.txt'
                sh 'cat file.txt'

            }
        }
    }
}