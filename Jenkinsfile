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
        docker network create todo-test-network || true

        docker run -d \
          --name todoappdb-test \
          --network todo-test-network \
          -e MARIADB_ROOT_PASSWORD=sekrit \
          -e MARIADB_DATABASE=todo_test_db \
          -e MARIADB_USER=todo_usr \
          -e MARIADB_PASSWORD=letmeinplz \
          mariadb:11

        echo "Waiting for MariaDB..."
        sleep 15

        docker exec -i todoappdb-test \
          mariadb -utodo_usr -pletmeinplz todo_test_db \
          < /var/jenkins_home/workspace/DotnetDemoPipeline/TodoApp/schema.sql

        docker run --rm \
          --network container:todoappdb-test \
          -v jenkins-data:/src \
          -w /src/workspace/DotnetDemoPipeline \
          mcr.microsoft.com/dotnet/sdk:10.0 \
          dotnet test TodoApp.Tests/TodoApp.Tests.csproj

        docker rm -f todoappdb-test
        docker network rm todo-test-network
    '''
}
}