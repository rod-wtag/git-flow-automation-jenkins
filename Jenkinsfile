pipeline {
    agent any

    triggers {
        githubPush()
    }

    environment {
        TAG_NAME = "r21.27.27"
        GIT_CREDENTIALS_ID = 'github-creds'
    }

    stage('Check Branch') {
        steps {
            script {
                if (env.GIT_BRANCH.contains('main')) {
                    echo "Detected main branch. Exiting pipeline as expected."
                    currentBuild.result = 'SUCCESS'
                    return
                } else {
                    echo "Not on main branch, continuing with pipeline execution"
                }
            }
        }
    }

    stages {
        stage('Get Version') {
            steps {
                script {
                    def versionFile = readFile('system/config/version.properties').trim()
                    def versionMatch = versionFile =~ /version:\s*(\d+\.\d+\.\d+)/
                    
                    if (versionMatch.find()) {
                        env.VERSION = versionMatch[0][1]
                        echo "Extracted version: ${env.VERSION}"
                    } else {
                        error "Could not extract version from properties file"
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