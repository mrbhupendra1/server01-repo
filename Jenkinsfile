pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/username/repo-name.git',
                    credentialsId: 'git-credentials'
            }
        }
    }
}
