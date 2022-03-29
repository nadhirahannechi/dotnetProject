pipeline {
     agent {
        docker {
            image 'mcr.microsoft.com/dotnet/sdk:6.0'
        }
     }
     environment {
    DOTNET_CLI_HOME = "/tmp/DOTNET_CLI_HOME"
}
     triggers {
        githubPush()
      }
    stages {
        // Restores the dependencies and tools of a project 
        stage('Restore packages'){
           steps{
               sh 'dotnet restore WebApplication.sln'
            }
         }
         // cleans the output of the previous build
        stage('Clean'){
           steps{
               sh 'dotnet clean WebApplication.sln --configuration Release'
            }
         }
        stage('Build'){
           steps{
               sh 'dotnet build WebApplication.sln --configuration Release --no-restore'
            }
         }
        stage('Test: Unit Test'){
           steps {
                sh 'dotnet test XUnitTestProject/XUnitTestProject.csproj --configuration Release --no-restore'
             }
          }
         // Publishes the application and its dependencies to a folder for deployment to a hosting system
        stage('Publish'){
             steps{
               sh 'dotnet publish WebApplication/WebApplication.csproj --configuration Release --no-restore'
             }
        }
         // Running the application in the background
        stage('Deploy'){
             steps{
               sh '''for pid in $(lsof -t -i:9090); do
                       kill -9 $pid
               done'''
               sh 'cd WebApplication/bin/Release/netcoreapp3.1/publish/'
               sh 'nohup dotnet WebApplication.dll --urls="http://104.128.91.189:9090" --ip="104.128.91.189" --port=9090 --no-restore > /dev/null 2>&1 &'
             }
        }        
    }
}
