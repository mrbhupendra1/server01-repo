pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/mrbhupendra1/server01-repo.git',
                    credentialsId: 'git-credentials'
            }
        }
    }
}
