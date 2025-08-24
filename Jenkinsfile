pipeline {
    agent any

    environment {
        MAVEN_HOME = '/opt/maven'
        JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto'
        PATH = "${MAVEN_HOME}/bin:${JAVA_HOME}/bin:${env.PATH}"
        SLACK_CHANNEL = 'jenkins-ci-pipeline-alerts-af'   // Your Slack channel name
        SLACK_CREDENTIAL_ID = 'your-slack-credential-id' // The Slack token stored in Jenkins credentials
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/AdenikeFaluwoye/Jenkins-Maven-SonarQube-Nexus.git', branch: 'main'
            }
        }

        stage('Validate') {
            steps {
                sh "${MAVEN_HOME}/bin/mvn validate"
            }
        }

        // Add other stages: Compile, Test, Package, SonarQube Analysis, etc.
    }

    post {
        success {
            slackSend(
                channel: "${SLACK_CHANNEL}",
                color: 'good',
                message: "Build Successful: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                tokenCredentialId: "${SLACK_CREDENTIAL_ID}"
            )
        }
        failure {
            slackSend(
                channel: "${SLACK_CHANNEL}",
                color: 'danger',
                message: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                tokenCredentialId: "${SLACK_CREDENTIAL_ID}"
            )
        }
    }
}
