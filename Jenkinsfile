pipeline {
    agent any
    triggers {
        githubPush()
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
    }
}