node {
    stage('Preparation') {
        checkout scm
    }

    stage('Check files') {
        sh '''
            echo "=== ROOT ==="
            ls -la

            echo "=== PROJECTS ==="
            find . -name "*.csproj" -o -name "*.sln"
        '''
    }
}