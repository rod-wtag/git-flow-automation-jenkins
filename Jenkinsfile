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
        stage('Checkout & Debug') {
            steps {
                checkout scm
                
                script {
                    // Print all environment variables for debugging
                    sh 'env | sort'
                    
                    // Try multiple methods to get branch name
                    echo "GIT_BRANCH: ${env.GIT_BRANCH ?: 'null'}"
                    echo "BRANCH_NAME (Jenkins): ${env.BRANCH_NAME ?: 'null'}"
                    echo "CHANGE_BRANCH: ${env.CHANGE_BRANCH ?: 'null'}"
                    echo "gitlabBranch: ${env.gitlabBranch ?: 'null'}"
                    
                    // Try different git commands
                    sh 'git branch'
                    sh 'git status'
                }
            }
        }
        
        stage('Set Branch Name') {
            steps {
                script {
                    // Try multiple methods to determine branch name
                    if (env.GIT_BRANCH) {
                        env.BRANCH_NAME = env.GIT_BRANCH.replaceAll('origin/', '')
                    } else if (env.CHANGE_BRANCH) {
                        env.BRANCH_NAME = env.CHANGE_BRANCH
                    } else if (env.gitlabBranch) {
                        env.BRANCH_NAME = env.gitlabBranch
                    } else {
                        try {
                            env.BRANCH_NAME = sh(script: "git branch --show-current", returnStdout: true).trim()
                        } catch (Exception e) {
                            echo "Error getting branch with git branch --show-current: ${e.message}"
                        }
                        
                        if (!env.BRANCH_NAME) {
                            try {
                                env.BRANCH_NAME = sh(script: "git name-rev --name-only HEAD", returnStdout: true).trim().replaceAll('remotes/origin/', '')
                            } catch (Exception e) {
                                echo "Error getting branch with git name-rev: ${e.message}"
                            }
                        }
                        
                        if (!env.BRANCH_NAME) {
                            try {
                                env.BRANCH_NAME = sh(script: "git rev-parse --abbrev-ref HEAD", returnStdout: true).trim()
                            } catch (Exception e) {
                                echo "Error getting branch with git rev-parse: ${e.message}"
                            }
                        }
                    }
                    
                    echo "Determined branch name: ${env.BRANCH_NAME ?: 'still null'}"
                    
                    // If we still don't have a branch name, set a default for testing
                    if (!env.BRANCH_NAME) {
                        echo "WARNING: Unable to determine branch name automatically"
                        // Option: Hardcode branch for testing
                        // env.BRANCH_NAME = "release/21.27"
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