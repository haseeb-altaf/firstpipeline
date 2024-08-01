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
        post {
        always {
            slackSend(channel: '#pipeline', color: 'good', message: "Pipeline finished for ${env.JOB_NAME} #${env.BUILD_NUMBER} (${env.BUILD_URL})")
        }
        success {
            slackSend(channel: '#pipeline', color: 'good', message: "Pipeline succeeded for ${env.JOB_NAME} #${env.BUILD_NUMBER} (${env.BUILD_URL})")
        }
        failure {
            slackSend(channel: '#pipeline', color: 'danger', message: "Pipeline failed for ${env.JOB_NAME} #${env.BUILD_NUMBER} (${env.BUILD_URL})")
    }
}
