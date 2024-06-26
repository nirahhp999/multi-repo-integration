pipeline {
    agent { label "master" }
    
    parameters {
        choice(
            name: 'REPO',
            choices: ['Repo1', 'Repo2', 'Repo3'],
            description: 'Select the repository to build'
        )
        string(
            name: 'BRANCH',
            defaultValue: 'main',
            description: 'Branch to build'
        )
    }

    environment {
        REPO1_URL = 'https://github.com/harishjadhav26/node_file_server.git'
        REPO2_URL = 'https://github.com/harishjadhav26/javaparser-maven-sample.git'
        REPO3_URL = 'https://github.com/harishjadhav26/githubactionproj.git'
    }

    stages {
        stage('Checkout') {
            steps {
                // Clean up workspace
                step([$class: 'WsCleanup'])
                script {
                    def repoUrl = ''
                    def targetDir = params.REPO

                    // Determine the repository URL
                    if (params.REPO == 'Repo1') {
                        repoUrl = env.REPO1_URL
                    } else if (params.REPO == 'Repo2') {
                        repoUrl = env.REPO2_URL
                    } else if (params.REPO == 'Repo3') {
                        repoUrl = env.REPO3_URL
                    } else {
                        error("Unknown repository: ${params.REPO}")
                    }

                    echo "Cloning ${repoUrl} branch ${params.BRANCH} into ${targetDir}"

                    // Verify branch existence
                    // def branchExists = sh (
                    //     script: "git ls-remote --heads ${repoUrl} ${params.BRANCH}",
                    //     returnStatus: true
                    // )
                    // Verify branch existence
                    // Verify branch existence with credentials
                    withCredentials([usernamePassword(credentialsId: 'github-token', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD')]) {
                        def authRepoUrl = repoUrl.replace('https://', "https://${GIT_USERNAME}:${GIT_PASSWORD}@")
                        def branchCheckCmd = "git ls-remote --heads ${authRepoUrl} refs/heads/${params.BRANCH}"
                        echo "Running command: ${branchCheckCmd}"
                        def branchExists = sh(script: branchCheckCmd, returnStdout: true).trim()

                        if (branchExists == '') {
                            error("Branch ${params.BRANCH} does not exist in repository ${repoUrl}")
                        } else {
                            echo "Branch exists: ${branchExists}"
                        }
                    }

                    // Clone the repository into the specific folder
                    dir(targetDir) {
                        try {
                            git url: repoUrl, branch: params.BRANCH, credentialsId: 'github-token-harish'
                        } catch (Exception e) {
                            echo "Error during git clone: ${e.message}"
                            currentBuild.result = 'FAILURE'
                            error("Failed to clone repository")
                        }
                    }

                    // Save repoUrl and targetDir in environment variables for use in subsequent stages
                    env.REPO_URL = repoUrl
                    env.TARGET_DIR = targetDir
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    if (env.TARGET_DIR == null) {
                        error("Target directory is not set")
                    }

                    dir("${env.TARGET_DIR}") {
                        echo "Building ${params.REPO} from branch ${params.BRANCH}, ${env.REPO_URL}"
                        sh "ls -ltrh ${WORKSPACE}/${env.TARGET_DIR}"
                        // Add your build steps here
                    }
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    if (env.TARGET_DIR == null) {
                        error("Target directory is not set")
                    }

                    dir("${env.TARGET_DIR}") {
                        echo "Testing ${params.REPO}"
                        sh "ls -ltrh ${WORKSPACE}/${env.TARGET_DIR}"
                        // Add your test steps here
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.TARGET_DIR == null) {
                        error("Target directory is not set")
                    }

                    dir("${env.TARGET_DIR}") {
                        echo "Deploying ${params.REPO}"
                        sh "ls -ltrh ${WORKSPACE}/${env.TARGET_DIR}"
                        // Add your deploy steps here
                    }
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up ${WORKSPACE}"
            // Add any cleanup steps here
        }
    }
}
