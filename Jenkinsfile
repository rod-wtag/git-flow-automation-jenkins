pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        BRANCH_NAME = "release/21.26"
        TAG_NAME = "r21.26.27"
        GIT_CREDENTIALS_ID = 'git-username'
    }

    stages {
        stage('Only on release/21.26') {
            when {
                branch 'release/21.26'
            }
            steps {
                echo "Triggered on release/21.26"
            }
        }

        stage('Create & Push Tag') {
            when {
                branch 'release/21.26'
            }
            steps {
                script {
                    sh """
                        git config user.name "rod-wtag"
                        git config user.email "roky.das@welldev.io"

                        git tag ${TAG_NAME}
                        git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/your-username/your-repo.git ${TAG_NAME}
                    """
                }
            }
        }
    }
}
pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        BRANCH_NAME = "release/21.26"
        TAG_NAME = "r21.26.27"
        GIT_CREDENTIALS_ID = 'git-username'
    }

    stages {
        stage('Only on release/21.26') {
            when {
                branch 'release/21.26'
            }
            steps {
                echo "Triggered on release/21.26"
            }
        }

        stage('Create & Push Tag') {
            when {
                branch 'release/21.26'
            }
            steps {
                script {
                    sh """
                        git config user.name "rod-wtag"
                        git config user.email "roky.das@welldev.io"

                        git tag ${TAG_NAME}
                        git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/your-username/your-repo.git ${TAG_NAME}
                    """
                }
            }
        }
    }
}
