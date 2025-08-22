def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger',
    'UNSTABLE': 'danger'
]

pipeline {
    agent any

    options {
        skipStagesAfterUnstable()
    }

    environment {
        MAVEN_HOME = '/opt/maven'
        PATH = "${MAVEN_HOME}/bin:${env.PATH}"
        JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto.x86_64' // Java 17 for build
    }

    stages {
        stage('Validate Project') {
            steps {
                echo "Validating project..."
                sh 'mvn validate'
            }
        }

        stage('Unit Test') {
            steps {
                echo "Running unit tests..."
                sh 'mvn test'
            }
        }

        stage('Integration Test') {
            steps {
                echo "Running integration tests..."
                sh 'mvn verify -DskipUnitTests'
            }
        }

        stage('App Packaging') {
            steps {
                echo "Packaging the application..."
                sh 'mvn package'

                // Archive artifact in Jenkins workspace even if later stages fail
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                echo "Artifact archived in Jenkins workspace."
            }
        }

        stage('Checkstyle Code Analysis') {
            steps {
                echo "Running Checkstyle code analysis..."
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('SonarQube Inspection') {
            steps {
                withEnv([
                    "JAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto.x86_64",
                    "PATH=/usr/lib/jvm/java-11-amazon-corretto.x86_64/bin:${env.PATH}"
                ]) {
                    echo "Running SonarQube scan with Java 17..."
                    sh """
                        mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.1.0.4751:sonar \
                            -Dsonar.projectKey=Java-WebApp-Project \
                            -Dsonar.host.url=http://172.31.80.181:9000 \
                            -Dsonar.login=eb9fdec1b30562172f674ba3d96c553ef2513e28
                    """
                }
            }
        }

        stage("Upload Artifact To Nexus") {
            steps {
                echo "Uploading artifact to Nexus..."
                sh 'mvn deploy'
            }
            post {
                success {
                    echo 'Successfully uploaded artifact to Nexus Artifactory'
                }
                failure {
                    echo 'Failed to upload artifact to Nexus Artifactory'
                }
            }
        }
    }

    post {
        always {
            echo 'Sending Slack Notification...'
            slackSend(
                channel: '#jenkins-ci-pipeline-alerts-af',
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job Name '${env.JOB_NAME}' build ${env.BUILD_NUMBER}\n" +
                         "Build Timestamp: ${env.BUILD_TIMESTAMP}\n" +
                         "Project Workspace: ${env.WORKSPACE}\n" +
                         "More info at: ${env.BUILD_URL}"
            )
        }
    }
}
