pipeline {
    agent any

    environment {
        GIT_REPOSITORY = 'https://github.com/yanamandrasri/NZWalks.git'
        AZURE_CREDENTIALS_ID = 'Azure_jenkins_SP'
        APP_NAME = 'qqq'
        RESOURCE_GROUP = 'qqq'
    }

// check Out stage pulls the code from Git Repo
    stages {
        stage('Checkout') {
            steps {
                git url: "${GIT_REPOSITORY}"
            }
        }

// Build stage will restore and build the asp.net application
        stage('Build') {
            steps {
                script {
                    // Restore dependencies
                    bat 'dotnet restore'
                    // Build the application
                    bat 'dotnet build --configuration Release'
                }
            }
        }

// Publish stage publish the build output
        stage('Publish') {
            steps {
                script {
                    // Publish the application
                    bat 'dotnet publish --configuration Release --output ./publish'
                }
            }
        }

// Deploy stage will deploy the application into azure web app.
        stage('Deploy to Azure') {
            steps {
                withCredentials([azureServicePrincipal(credentialsId: "${AZURE_CREDENTIALS_ID}")]) {
                    script {
                        // Install Azure CLI if not already installed
                        bat 'az --version'
                        
                        // Login to Azure
                        bat '''
                        az login --service-principal -u %AZURE_CLIENT_ID% -p %AZURE_CLIENT_SECRET% --tenant %AZURE_TENANT_ID%
                        az webapp deployment source config-zip --resource-group ${RESOURCE_GROUP} --name ${APP_NAME} --src ./publish.zip
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment succeeded!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
