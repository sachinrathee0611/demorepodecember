pipeline {
    agent any

    environment {
        SLACK_CHANNEL = '#my-channel' // Slack channel for notifications
    }

    stages {
        // Retrieve artifacts from upstream job
        stage('Retrieve Artifacts') {
            steps {
                copyArtifacts(
                    projectName: 'Upstream-Build', // Replace with your upstream pipeline name
                    filter: 'target/*.war',        // Specify the artifact(s) you want to copy
                    target: 'target'              // Directory to copy artifacts to
                )
            }
        }

        // Deploy the application based on the branch
        stage('Deploy') {
            when {
                anyOf {
                    branch 'main'
                    branch 'testing'
                    branch 'development'
                }
            }
            steps {
                script {
                    if (env.BRANCH_NAME == 'main') {
                        echo "Deploying to production (prod) environment..."
                        sh './deploy-prod.sh'  // Example production deploy script
                    } else if (env.BRANCH_NAME == 'testing') {
                        echo "Deploying to testing environment..."
                        sh './deploy-staging.sh'  // Example staging deploy script
                    } else if (env.BRANCH_NAME.startsWith('development')) {
                        echo "Deploying to development environment for ${env.BRANCH_NAME}..."
                        sh './deploy-beta.sh'  // Example beta-specific deploy script
                    }
                }
            }
        }
    }

    post {
        success {
            slackSend(
                channel: SLACK_CHANNEL, 
                message: "Downstream Build ${env.BUILD_ID} completed successfully :tada:", 
                color: 'good'
            )
        }
        failure {
            slackSend(
                channel: SLACK_CHANNEL, 
                message: "Downstream Build ${env.BUILD_ID} failed. Please check the logs! :x:", 
                color: 'danger'
            )
        }
    }
}
