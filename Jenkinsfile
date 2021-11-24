pipeline {
    agent any
    triggers {
        githubPush()
    }
    stages {
        stage('Restore packages'){
           steps{
               sh 'dotnet restore CleanArchitecture.sln'
            }
         }
        stage('Clean'){
           steps{
               sh 'dotnet clean CleanArchitecture.sln --configuration Release'
            }
         }
        stage('Build'){
           steps{
               sh 'dotnet build CleanArchitecture.sln --configuration Release --no-restore'
            }
         }
        stage('Test: Unit Test'){
           steps {
                sh 'dotnet test CleanArchitecture.sln --configuration Release --no-restore'
             }
          }
        stage('Publish'){
             steps{
               sh 'dotnet publish src/WebUI/WebUI.csproj --configuration Release --no-restore'
             }
        }
        stage('Deploy'){
             steps{
               sh '''for pid in $(lsof -t -i:9090); do
                       kill -9 $pid
               done'''
               sh 'cd src/WebUI/bin/Release/net6.0/publish/'
               sh 'nohup dotnet WebUI.dll --urls="http://104.128.91.189:9090" --ip="104.128.91.189" --port=9090 --no-restore > /dev/null 2>&1 &'
             }
        }
    }
}