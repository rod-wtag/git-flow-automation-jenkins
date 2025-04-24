pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        BRANCH_NAME = ""
        TAG_NAME = "r21.27.27"
        GIT_CREDENTIALS_ID = 'github-creds'
    }

    stages {
        stage('Checkout & Set Branch Name') {
            steps {
                checkout scm
                
                script {
                    // Extract branch name from GIT_BRANCH by removing 'origin/' prefix
                    if (env.GIT_BRANCH) {
                        env.BRANCH_NAME = env.GIT_BRANCH.replaceAll('origin/', '')
                        echo "Current branch: ${env.GIT_BRANCH}"
                        echo "Current branch: ${env.BRANCH_NAME}"
                    } else {
                        echo "GIT_BRANCH is null, unable to determine branch name"
                    }
                }
            }
        }

        stage('Tag & Push') {
            when {
                expression {
                    return env.BRANCH_NAME == 'release/21.27'
                }
            }
            steps {
                echo "Running Tag & Push for branch: ${env.BRANCH_NAME}"
                withCredentials([usernamePassword(credentialsId: 'github-creds', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_TOKEN')]) {
                    sh '''
                        git config user.name "rod-wtag"
                        git config user.email "roky.das@welldev.io"

                        git tag -d ${TAG_NAME} || true
                        git tag -f ${TAG_NAME}
                        git push https://$GIT_USERNAME:$GIT_TOKEN@github.com/rod-wtag/git-flow-automation-jenkins.git $TAG_NAME
                    '''
                }
            }
        }

        stage('Merge Tag to release/21.28') {
            when {
                expression {
                    return env.BRANCH_NAME == 'release/21.27'
                }
            }
            steps {
                echo "Running Merge Tag for branch: ${env.BRANCH_NAME}"
                withCredentials([usernamePassword(credentialsId: 'github-creds', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_TOKEN')]) {
                    script {
                        sh '''
                            git fetch --all
                            git branch -D release-21.28-local || true
                            git checkout origin/release/21.28 -b release-21.28-local

                            # Merge the tag into release/21.28
                            git merge ${TAG_NAME} -m "Merge tag ${TAG_NAME} into release/21.28"

                            # Push the updated branch back
                            git push https://${GIT_USERNAME}:${GIT_TOKEN}@github.com/rod-wtag/git-flow-automation-jenkins.git HEAD:release/21.28
                        '''
                    }
                }
            }
        }
    }
    
    post {
        always {
            echo "Pipeline completed. Final branch name: ${env.BRANCH_NAME}"
        }
    }
}