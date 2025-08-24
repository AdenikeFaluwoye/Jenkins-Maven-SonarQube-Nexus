pipeline {
    agent any

    environment {
        MAVEN_HOME = '/opt/maven'
        JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto'
        PATH+EXTRA = "${MAVEN_HOME}/bin:${JAVA_HOME}/bin"
        SLACK_CHANNEL = 'jenkins-ci-pipeline-alerts-af'
        SLACK_CREDENTIAL_ID = 'your-slack-credential-id'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/AdenikeFaluwoye/Jenkins-Maven-SonarQube-Nexus.git', branch: 'main'
            }
        }

        stage('Validate') {
            steps {
                sh 'mvn validate'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_HOST_URL = 'http://your-sonarqube-server:9000'
                SONAR_LOGIN = credentials('your-sonar-token-id')
            }
            steps {
                sh 'mvn sonar:sonar -Dsonar.projectKey=your-project-key -Dsonar.host.url=$SONAR_HOST_URL -Dsonar.login=$SONAR_LOGIN'
            }
        }

        stage('Deploy to Nexus') {
            steps {
                sh 'mvn deploy'
            }
        }
    }

    post {
        success {
            slackSend(
                channel: "${SLACK_CHANNEL}",
                color: 'good',
                message: "✅ Build Successful: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                tokenCredentialId: "${SLACK_CREDENTIAL_ID}"
            )
        }
        failure {
            slackSend(
                channel: "${SLACK_CHANNEL}",
                color: 'danger',
                message: "❌ Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                tokenCredentialId: "${SLACK_CREDENTIAL_ID}"
            )
        }
    }
}
