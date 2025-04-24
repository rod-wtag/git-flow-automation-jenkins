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
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Set Branch Name') {
            steps {
                script {
                    // Use the Jenkins built-in env variable or retrieve from git
                    env.BRANCH_NAME = env.GIT_BRANCH ? env.GIT_BRANCH.replaceAll('origin/', '') : 
                                      sh(script: "git name-rev --name-only HEAD", returnStdout: true).trim()
                    echo "Current branch: ${env.BRANCH_NAME}"
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
}
