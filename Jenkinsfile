def repoUrl = ''
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
                step([$class: 'WsCleanup'])
                checkout scm
                sh "ls -ltrh ${WORKSPACE}"
                script {
                    // def repoUrl = ''    
                    def targetDir = "${params.REPO}"
                    
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

                    // Clone the repository into the specific folder
                    dir(targetDir) {
                        git url: repoUrl, branch: params.BRANCH, credentialsId: 'github-token-harish' 
                    }
                }
            }
        }

        stage('Build') {
            steps {
                dir('repository') {
                    echo "Building ${params.REPO} from branch ${params.BRANCH}, ${repoUrl}"
                    // Add your build steps here
                }
            }
        }

        stage('Test') {
            steps {
                dir('repository') {
                    echo "Testing ${params.REPO}"
                    // Add your test steps here
                }
            }
        }

        stage('Deploy') {
            steps {
                dir('repository') {
                    echo "Deploying ${params.REPO}"
                    // Add your deploy steps here
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up"
            // Add any cleanup steps here
        }
    }
}
