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
            when {
                branch 'develop'
            }
            steps {
                script {
                    if (env.CHANGE_ID) {
                        echo 'This is a Pull Request build for the develop branch'
                    } else {
                        echo 'This is a push build to the develop branch'
                    }
                }
            }
        }

        stage('Docker Build') {
            when {
                branch 'develop'
            }
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Docker Push') {
            when {
                branch 'develop'
            }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-credentials') {
                        docker.image(DOCKER_IMAGE).push('latest')
                    }
                }
            }
        }
    }

    post {
        success {
            slackSend(channel: "${env.SLACK_CHANNEL}", color: 'good', message: "Build succeeded: ${env.BUILD_URL}")
        }
        failure {
            slackSend(channel: "${env.SLACK_CHANNEL}", color: 'danger', message: "Build failed: ${env.BUILD_URL}")
        }
        unstable {
            slackSend(channel: "${env.SLACK_CHANNEL}", color: 'warning', message: "Build is unstable: ${env.BUILD_URL}")
        }
    }
}
