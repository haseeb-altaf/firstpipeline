pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'haseeb497/techworld-demo-app'
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        SLACK_CREDENTIALS_ID = 'slack-creds' 
        SLACK_CHANNEL = '#pipeline'
    }

    stages {
        stage('Build') {
            steps {
                script {
                    if (env.CHANGE_ID) {
                        echo 'This is a Pull Request build'
                    } else {
                        echo 'This is a push build'
                    }
                }
            }
        }

        stage('Docker Build') {
            when {
                expression { return !env.CHANGE_ID } // Only run on push events, not PRs
            }
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Docker Push') {
            when {
                expression { return !env.CHANGE_ID } // Only run on push events, not PRs
            }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials-id') {
                        docker.image(DOCKER_IMAGE).push('latest')
                    }
                }
            }
        }
    }

    post {
        success {
            slackSend(channel: SLACK_CHANNEL, message: "Pipeline succeeded for ${env.JOB_NAME} ${env.BUILD_NUMBER}", credentialsId: SLACK_CREDENTIALS_ID)
        }
        failure {
            slackSend(channel: SLACK_CHANNEL, message: "Pipeline failed for ${env.JOB_NAME} ${env.BUILD_NUMBER}", credentialsId: SLACK_CREDENTIALS_ID)
        }
    }
}
