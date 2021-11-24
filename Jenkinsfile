pipeline {
    agent any
    triggers {
        githubPush()
    }

   parameters {
      booleanParam(name: 'CLEAN_WORKSPACE', defaultValue: true, description: 'Clean workspace')
      booleanParam(name: 'TESTING_FRONTEND', defaultValue: true, description: 'Testing frontend')
   }

   environment {
      ON_SUCCESS_SEND_EMAIL = 'true'
      ON_FAILURE_SEND_EMAIL = 'true'
   }

    stages {
        stage('Restore packages'){
           steps{
               bat 'dotnet restore CleanArchitecture.sln'
            }
         }
        stage('Clean'){
           steps{
               bat 'dotnet clean CleanArchitecture.sln --configuration Release'
            }
         }
        stage('Build'){
           steps{
               bat 'dotnet build CleanArchitecture.sln --configuration Release --no-restore'
            }
         }
        stage('Test: Unit Test'){
           steps {
                bat 'dotnet test CleanArchitecture.sln --configuration Release --no-restore -l:trx;LogFileName="TestOutput.xml"'
             }
          }
        stage('Publish'){
             steps{
               bat 'dotnet publish src/WebUI/WebUI.csproj --configuration Release --no-restore'
             }
        }
        stage("Send email"){
           steps{
            emailext body: 'Test Message',
               subject: 'Test Subject',
               to: 'octavian.mitu@amdaris.com'
           }
        }
    }
    post {
      script {
         if (CLEAN_WORKSPACE == 'true') {
            echo 'Clean workspace'
            cleanWs()
         } else {
            echo 'Workspace was not cleaned'
         }
         echo TESTING_FRONTEND
      }
    }
}