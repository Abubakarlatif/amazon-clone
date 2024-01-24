pipeline {
    agent any

    environment {
        GITHUB_REPO_URL = 'https://github.com/WaQQass/CICD-firstPipeline'
        GITHUB_CREDENTIALS_ID = '001'  // Replace with your GitHub credentials ID
        REMOTE_HOST = '13.201.84.0'
        REMOTE_USER = 'ubuntu'
        SSH_CREDENTIALS_ID = 'newJenkinshost13.201.84.0'
        LOCAL_APP_DIR = "${JENKINS_HOME}/workspace/CICD_New_Waqas"
    }

    triggers {
        githubPush()
    }

    stages {
        stage('Hello from Jenkins') {
            steps {
                script {
                    echo 'Hello from Jenkins!'
                }
            }
        }
        stage('Connect to Remote EC2') {
            steps {
                script {
                    // Use withCredentials to securely handle the SSH private key
                    withCredentials([sshUserPrivateKey(credentialsId: SSH_CREDENTIALS_ID, keyFileVariable: 'PEM_FILE_PATH')]) {
                        // Create the local directory if it doesn't exist
                        

                        // Remove the existing directory before cloning
                       // sh "ssh -o StrictHostKeyChecking=no -i ${PEM_FILE_PATH} ${REMOTE_USER}@${REMOTE_HOST} 'sudo rm -rf ${LOCAL_APP_DIR}'"

                        // Execute commands on the remote EC2 instance
                        sh "ssh -o StrictHostKeyChecking=no -i ${PEM_FILE_PATH} ${REMOTE_USER}@${REMOTE_HOST} 'mkdir two.txt'"
                        sh "ssh -o StrictHostKeyChecking=no -i ${PEM_FILE_PATH} ${REMOTE_USER}@${REMOTE_HOST} 'pwd'"
                    }
                }
            }
        }
        stage('Deploy to EC2') {
            steps {
                script {
                    // Fetch changes from GitHub
                    checkout([$class: 'GitSCM', branches: [[name: 'main']], userRemoteConfigs: [[url: "${GITHUB_REPO_URL}", credentialsId: "${GITHUB_CREDENTIALS_ID}"]]])

                    // Deploy to the EC2 host
                    withCredentials([sshUserPrivateKey(credentialsId: SSH_CREDENTIALS_ID, keyFileVariable: 'PEM_FILE_PATH')]){
                        sh "ssh -o StrictHostKeyChecking=no -i ${PEM_FILE_PATH} ${REMOTE_USER}@${REMOTE_HOST} 'ls -la ${LOCAL_APP_DIR}'"
                        sh "ssh -o StrictHostKeyChecking=no -i ${PEM_FILE_PATH} ${REMOTE_USER}@${REMOTE_HOST} 'sudo ls -la /var/www/html/'"
                        sh "ssh -o StrictHostKeyChecking=no -i ${PEM_FILE_PATH} ${REMOTE_USER}@${REMOTE_HOST} 'sudo rm -rf /var/www/html/*'"
                        sh "ssh -o StrictHostKeyChecking=no -i ${PEM_FILE_PATH} ${REMOTE_USER}@${REMOTE_HOST} 'sudo cp -r ${WORKSPACE}/* /var/www/html/'"
                    }
                }
            }
        }

        // Add more stages as needed
    }
}
