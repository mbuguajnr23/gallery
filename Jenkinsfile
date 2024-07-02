pipeline {
    agent any

    tools {
        nodejs 'NodeJS 22.3.0' // Use the name you configured in the Global Tool Configuration
    }
    environment {
        RENDER_APP_NAME = 'gallery' // Replace with your Render application name
        RENDER_LINK = 'https://gallery-95mu.onrender.com/' // Replace with your Render application link
        SLACK_CHANNEL = 'ianip1' // Replace with your Slack channel
        SLACK_CREDENTIALS_ID = '7UT8vmhodgwdjeDPLPyd4RI3' // Ensure this matches your Jenkins credentials ID
        EMAIL_RECIPIENT = 'mbuguaian32@gmail.com' // Replace with your email recipient or ensure it's set in Jenkins
    }

    triggers {
        pollSCM('H/2 * * * *') // Polls the SCM every 2 minutes
    }

    stages {
        stage("Clone gallery repository") {
            steps {
                git branch: 'master', url: 'https://github.com/mbuguajnr23/gallery.git'
            }
        }

        stage('Install dependencies') {
            steps {
                script {
                    try {
                        sh 'npm install'
                    } catch (Exception e) {
                        error "Failed to install dependencies: ${e.message}"
                    }
                }
            }
        }

        stage('Test project') {
            steps {
                script {
                    try {
                        echo 'Running tests...'
                        sh 'npm test'
                    } catch (Exception e) {
                        error "Tests failed: ${e.message}"
                    }
                }
            }
        }

        stage('Build project') {
            steps {
                script {
                    try {
                        echo 'Building project...'
                        sh 'npm run build'
                    } catch (Exception e) {
                        error "Build failed: ${e.message}"
                    }
                }
            }
        }

        stage('Start server') {
            steps {
                script {
                    try {
                        echo 'Starting server...'
                        sh 'npm start &'
                        sleep 10 // Give time for the server to start
                    } catch (Exception e) {
                        error "Failed to start server: ${e.message}"
                    }
                }
            }
        }

        stage('Deploy to Render') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'render_api_key', variable: 'RENDER_API_KEY')]) {
                        try {
                            sh """
                            curl -X POST -H 'Authorization: Bearer ${RENDER_API_KEY}' \
                            -H 'Content-Type: application/json' \
                            -d '{"branch": "master", "env": {"NODE_ENV": "production"}}' \
                            https://api.render.com/v1/services/${RENDER_APP_NAME}/deploy
                            """
                        } catch (Exception e) {
                            error "Deployment to Render failed: ${e.message}"
                        }
                    }
                }
            }
        }
    }

    post {
        success {
            script {
                echo 'Deployment to Render succeeded!'
                slackSend (
                    channel: "${SLACK_CHANNEL}",
                    color: 'good',
                    message: "Build succeeded: ${env.JOB_NAME} ${env.BUILD_NUMBER}. Access app on ${RENDER_LINK}"
                )
            }
        }

        failure {
            script {
                echo 'Deployment to Render failed!'
                slackSend (
                    channel: "${SLACK_CHANNEL}",
                    color: 'danger',
                    message: "Build failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}"
                )
            }
        }

        always {
            script {
                if (currentBuild.result == 'FAILURE') {
                    emailext (
                        to: "${EMAIL_RECIPIENT}",
                        subject: "Jenkins Build Failed: ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                        body: """
                        <p>The Jenkins build <b>${env.JOB_NAME} ${env.BUILD_NUMBER}</b> has failed.</p>
                        <p>Please check the Jenkins console output for more details: ${env.BUILD_URL}</p>
                        """
                    )
                }
            }
        }
    }
}
