node {
    stage('Preparation') {
        checkout scm
    }

    stage('Build') {
        sh '''
            docker run --rm \
              -v "$WORKSPACE:/src" \
              -w /src \
              mcr.microsoft.com/dotnet/sdk:10.0 \
              sh -c "ls -la && find . -name '*.csproj' && dotnet build ./TodoApp/TodoApp.csproj"
        '''
    }

    stage('Test') {
        sh '''
            docker run --rm \
              -v "$WORKSPACE:/src" \
              -w /src \
              mcr.microsoft.com/dotnet/sdk:10.0 \
              sh -c "dotnet test ./TodoApp.Tests/TodoApp.Tests.csproj"
        '''
    }
}