pipeline {
    agent any
    
    triggers {
        githubPush()
    }

   parameters {
      booleanParam(name: 'CLEAN_WORKSPACE', defaultValue: true, description: 'Clean workspace')
      booleanParam(name: 'TESTING_FRONTEND', defaultValue: true, description: 'Testing frontend')
      string(name: 'USERNAME', defaultValue: '', description:'Docker username')
      string(name: 'PASSWORD', defaultValue: '', description:'Docker password')
      string(name: 'AZ_USERNAME', defaultValue: '', description:'Azure e username')
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
		stage('Test Frontend'){
             steps{
               echo "${params.TESTING_FRONTEND}"
             }
        }
      stage('Create Docker Image') {
         steps {
            script {
               node {
                     checkout scm
                     bat "docker login -u ${params.USERNAME} -p ${params.PASSWORD}"
                     docker.build("octavianmitu/cleanarhitecture:${env.BUILD_NUMBER}").push()
                     docker.build("octavianmitu/cleanarhitecture-frontend:${env.BUILD_NUMBER}").push()
                     bat "docker compose up -d"
                     // bat "az login -u ${params.AZ_USERNAME} -p ${params.PASSWORD}"
                     // bat "az vm run-command invoke --resource-group dev --name octavian-vm --command-id RunShellScript --scripts \"cd /home/octavian/CleanArhitecture && docker-compose up\""
                  }
               }
            }
         }
      }
    post {
      always {
         script {
            if (params.CLEAN_WORKSPACE == 'true') {
               echo 'Clean workspace'
               cleanWs()
            } else {
               echo 'Workspace was not cleaned'
            }
            bat "docker compose down"
         }
      }
    }
}