pipeline {
    agent any

    tools {
        nodejs 'NodeJS 22.3.0' // Use the name you configured in the Global Tool Configuration
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Install dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test project') {
            steps {
                script {
                    echo "Running tests..."
                    // Add your test commands here
                }
            }
        }

        stage('Build project') {
            steps {
                script {
                    echo "Building project..."
                    // Add your build commands here
                }
            }
        }

        stage('Start server') {
            steps {
                script {
                    echo "Starting server..."
                    // Add commands to start your server
                }
            }
        }

        stage('Deploy to Render') {
            steps {
                script {
                    echo "Deploying to Render..."
                    // Add your deployment steps here
                }
            }
        }
    }

    post {
        failure {
            emailext(
                subject: "Deployment to Render failed!",
                body: "Deployment to Render failed at ${env.BUILD_URL}",
                to: "mbuguaian32@gmail.com"
            )
            slackSend (
                channel: 'ianip1',
                color: 'danger',
                message: "Deployment to Render failed for build ${env.BUILD_URL}"
            )
        }
    }
}
