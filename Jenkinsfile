pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        GIT_CREDENTIALS_ID = 'github-creds'
    }

    stages {
        stage('adding username and email') {
            steps {
                script {
                    sh """
                        git config user.name "rod-wtag"
                        git config user.email "roky.das@welldev.io"
                    """
                }
            }
        }
        

        // stage('Check Branch') {
        //     steps {
        //         script {
        //             if (!env.GIT_BRANCH.contains('release/')) {
        //                 echo "Not detected release branch. Stopping pipeline execution."
        //                 currentBuild.result = 'ABORTED'
        //                 error "Pipeline stopped because it's not running on release branch"
        //             } else {
        //                 echo "It's a release branch, continuing with pipeline execution"
        //             }
        //         }
        //     }
        // }

        stage('Get and Bump Version') {
            steps {
                script {
                    def versionFilePath = 'system/config/version.properties'

                    // Extract the version line
                    def versionLine = versionFileContent.readLines().find { it.startsWith('version:') }
                    if (!versionLine) {
                        error "version: line not found in properties file"
                    }

                    // Parse version numbers using split
                    def versionStr = versionLine.split(':')[1].trim()
                    def (major, minor, patch) = versionStr.split('\\.').collect { it.toInteger() }
                    sh """
                        echo "Current version: ${major}.${minor}.${patch}"
                    """
                }
            }
        }


        // stage('Tag & Push') {
        //     when {
        //         expression {
        //             return env.BRANCH_NAME == 'release/21.27'
        //         }
        //     }
        //     steps {
        //         echo "Running Tag & Push for branch: ${env.BRANCH_NAME}"
        //         withCredentials([usernamePassword(credentialsId: 'github-creds', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_TOKEN')]) {
        //             sh '''
        //                 git config user.name "rod-wtag"
        //                 git config user.email "roky.das@welldev.io"

        //                 git tag -d ${TAG_NAME} || true
        //                 git tag -f ${TAG_NAME}
        //                 git push https://$GIT_USERNAME:$GIT_TOKEN@github.com/rod-wtag/git-flow-automation-jenkins.git $TAG_NAME
        //             '''
        //         }
        //     }
        // }


        // stage('Merge Tag to release/21.28') {
        //     when {
        //         expression {
        //             return env.BRANCH_NAME == 'release/21.27'
        //         }
        //     }
        //     steps {
        //         echo "Running Merge Tag for branch: ${env.BRANCH_NAME}"
        //         withCredentials([usernamePassword(credentialsId: 'github-creds', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_TOKEN')]) {
        //             script {
        //                 sh '''
        //                     git fetch --all
        //                     git branch -D release-21.28-local || true
        //                     git checkout origin/release/21.28 -b release-21.28-local

        //                     # Merge the tag into release/21.28
        //                     git merge ${TAG_NAME} -m "Merge tag ${TAG_NAME} into release/21.28"

        //                     # Push the updated branch back
        //                     git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/rod-wtag/git-flow-automation-jenkins.git HEAD:release/21.28
        //                 '''
        //             }
        //         }
        //     }
        // }
    }
}