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

            docker rm -f todoappdb-test >/dev/null 2>&1 || true

            docker run -d \
              --name todoappdb-test \
              --network todo-test-network \
              --health-cmd='healthcheck.sh --connect --innodb_initialized' \
              --health-interval=2s \
              --health-timeout=5s \
              --health-retries=30 \
              -e MARIADB_ROOT_PASSWORD=sekrit \
              -e MARIADB_DATABASE=todo_test_db \
              -e MARIADB_USER=todo_usr \
              -e MARIADB_PASSWORD=letmeinplz \
              mariadb:11

            echo "Waiting for MariaDB..."

            until [ "$(docker inspect --format='{{.State.Health.Status}}' todoappdb-test)" = "healthy" ]; do
                sleep 2
            done

            echo "MariaDB is ready!"

            docker exec todoappdb-test \
              mariadb -uroot -psekrit \
              -e "CREATE DATABASE IF NOT EXISTS todo_db; GRANT ALL PRIVILEGES ON todo_db.* TO 'todo_usr'@'%'; FLUSH PRIVILEGES;"

            docker exec jenkins_server \
              cat /var/jenkins_home/workspace/DotnetDemoPipeline/TodoApp/schema.sql \
              | docker exec -i todoappdb-test \
              mariadb -utodo_usr -pletmeinplz todo_test_db

            docker exec jenkins_server \
              cat /var/jenkins_home/workspace/DotnetDemoPipeline/TodoApp/schema.sql \
              | docker exec -i todoappdb-test \
              mariadb -utodo_usr -pletmeinplz todo_db

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

