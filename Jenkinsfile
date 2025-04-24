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
                    def versionFileContent = readFile(versionFilePath).trim()
                    def versionMatch = versionFileContent =~ /version:\s*(\d+)\.(\d+)\.(\d+)/

                    if (versionMatch.find()) {
                        def major = versionMatch[0][1].toInteger()
                        def minor = versionMatch[0][2].toInteger()
                        def patch = versionMatch[0][3].toInteger()

                        patch += 1
                        def newVersion = "${major}.${minor}.${patch}"
                        env.VERSION = newVersion
                        echo "Bumped version: ${env.VERSION}"

                        // Replace the version line in the file
                        def updatedContent = "version: ${newVersion}"
                        writeFile(file: versionFilePath, text: updatedContent)
                        echo "Updated version.properties file with new version."

                        // Git commit and push
                        // sh """
                        //     git config user.name "rod-wtag"
                        //     git config user.email "roky.das@welldev.io"
                        //     git add ${versionFilePath}
                        //     git commit -m "Bump version to ${env.VERSION}"
                        //     git push origin HEAD
                        // """
                    } else {
                        error "Could not extract version from properties file"
                    }
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