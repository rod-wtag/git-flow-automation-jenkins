// pipeline {
//     agent any

//     triggers {
//         githubPush() // Automatically triggers on push via webhook
//     }

//     stages {
//         stage('Say Hello') {
//             steps {
//                 sh 'echo "hello world"'
//             }
//         }
//     }
// }


pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        BRANCH_NAME = "release/21.27"
        TAG_NAME = "r21.27.27"
        GIT_CREDENTIALS_ID = 'github-creds'
    }

    stages {
        stage('Only on release/21.27') {
            when {
                branch 'release/21.27'
            }
            steps {
                echo "Triggered on release/21.27"
            }
        }

        // stage('Create & Push Tag') {
        //     when {
        //         branch 'release/21.27'
        //     }
        //     steps {
        //         script {
        //             sh """
        //                 git config user.name "rod-wtag"
        //                 git config user.email "roky.das@welldev.io"

        //                 git tag ${TAG_NAME}
        //                 git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/your-username/your-repo.git ${TAG_NAME}
        //             """
        //         }
        //     }
        // }
    }
}
