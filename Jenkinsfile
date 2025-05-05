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
                sh 'trivy fs --exit-code 1 --severity CRITICAL,HIGH,LOW --format table --output trivy-report.txt --ignore-unfixed --ignorefile .trivyignore .'               
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