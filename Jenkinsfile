pipeline {
    agent any

    triggers {
        githubPush() // Automatically triggers on push via webhook
    }

    stages {
        stage('Say Hello') {
            steps {
                sh 'echo "hello world"'
            }
        }
    }
}
