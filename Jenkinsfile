pipeline {
    agent any
    tools{
        maven 'maven-proj'
    }
    stages {
        stage('Checkout from git'){
            steps{
                echo "checking out from git"
                git branch:'master', url:'https://github.com/kaurjkg/petclinic.git'
            }
        }
        stage('Maven Compile') {
            steps {
                echo 'This is maven compile stage'
                sh 'mvn compile'
            }
        }
        stage('Trivy file system scan'){ 
            steps{
                echo 'Trivy file system scan started'
                sh 'trivy fs --format table --output trivy-report.txt --severity HIGH,CRITICAL .'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
    }
}           