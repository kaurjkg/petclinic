pipeline {
    agent any
    tools{
        maven 'maven-proj'
    }
    environment {
        // Define the Docker image name and tag
        IMAGE_NAME        = "petclinic"
        IMAGE_TAG         = "${BUILD_NUMBER}" // Use build number as version
        // Define the Azure Container Registry name
        ACR_NAME          = "jkgacr"
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
        //ALso skipped adding webhook for sonar quality gate

        stage('Build using maven package'){
            steps{
                echo 'This is maven package stage'
                sh 'mvn package'
            }
        }
        stage('Build docker image'){
            steps{
                echo 'Building docker image'
                script{
                    docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                    echo 'Docker image built successfully'
                }
            }

        }
        stage('Install Azure CLI') {
            steps {
                sh '''
                    curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
                    echo 'Azure CLI installed successfully'
                '''
            }
        }
        stage('Login to Azure using Managed Identity') {
            steps {
                echo 'Logging in to Azure using Managed Identity'
                sh '''
                    az login --identity
                    az acr login --name $ACR_NAME
                '''
            }
        }
        stage('Docker Push to ACR') {
            steps {
                script {
                    echo "Docker Push Started"
                    sh '''
                        docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${FULL_IMAGE_NAME}
                        docker push ${FULL_IMAGE_NAME}
                    '''
                }
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