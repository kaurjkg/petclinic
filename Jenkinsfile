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
        stage('Sonar Analysis') {
            environment {
                SCANNER_HOME = tool 'sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonar-server') {
                    // using "''' to write multi-line shell script"
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.organization=jasjkg \
                        -Dsonar.projectName=petclinic \
                        -Dsonar.projectKey=jasjkg_petclinic \
                        -Dsonar.java.binaries=. \
                        -Dsonar.exclusions=**/trivy-fs-output.txt
                    '''
                }
            }
        }
    // SOnar quality gate not working because basic plan doesn't allow to create custom quality gate
        // stage('Sonar Quality Gate') {
        //     steps {
        //         timeout(time: 1, unit: 'MINUTES') {
        //             waitForQualityGate abortPipeline: true, credentialsId: 'sonarqubetoken'
        //         }
        //     }
        // }
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