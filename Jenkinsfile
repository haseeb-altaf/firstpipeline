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
                anyOf {
                    branch 'develop'
                    changeset '*'
                }
            }
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
                anyOf {
                    branch 'develop'
                    changeset '*'
                }
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
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
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
