node {
    stage('Preparation') {
        checkout scm
    }

    stage('Build') {
        sh '''
            docker run --rm \
              -v jenkins-data:/src \
              -w /src/workspace/DotnetDemoPipeline \
              mcr.microsoft.com/dotnet/sdk:10.0 \
              dotnet build TodoApp/TodoApp.csproj
        '''
    }

    stage('Test') {
        sh '''
            docker run --rm \
              -v jenkins-data:/src \
              -w /src/workspace/DotnetDemoPipeline \
              mcr.microsoft.com/dotnet/sdk:10.0 \
              dotnet test TodoApp.Tests/TodoApp.Tests.csproj
        '''
    }
}