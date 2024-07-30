pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials' // Jenkins ID for Docker Hub credentials
        SLACK_CHANNEL = '#pipeline' // Your Slack channel
        SLACK_CREDENTIALS_ID = 'slack-creds' // Jenkins ID for Slack credentials
        DOCKER_IMAGE = 'firstpipeline' // Name for your Docker image
    }

    triggers {
        gitlab(triggerOnPush: true, triggerOnMergeRequest: true)
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test'
            }
        }

        stage('Run Migrations') {
            steps {
                sh 'npm run migrate' // Adjust this command based on your migration script
            }
        }

        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}:${env.BUILD_ID}")
                }
            }
        }

        stage('Push Docker Image') {
            when {
                branch 'develop'
            }
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${DOCKER_CREDENTIALS_ID}") {
                        docker.image("${DOCKER_IMAGE}:${env.BUILD_ID}").push('latest')
                        docker.image("${DOCKER_IMAGE}:${env.BUILD_ID}").push("${env.BUILD_ID}")
                    }
                }
            }
        }

        stage('Notify Slack') {
            steps {
                slackSend (
                    channel: "${SLACK_CHANNEL}",
                    message: "Pipeline completed successfully for build ${env.BUILD_ID}",
                    teamDomain: 'yourteam',
                    tokenCredentialId: "${SLACK_CREDENTIALS_ID}"
                )
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
