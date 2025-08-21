def COLOR_MAP = [
    'SUCCESS': 'good',
    'FAILURE': 'danger',
    'UNSTABLE': 'danger'
]

pipeline {
    agent any
    environment {
        MAVEN_HOME = '/opt/maven'                            // your Maven installation
        PATH = "${MAVEN_HOME}/bin:${env.PATH}"               // ensures 'mvn' command is visible
        JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto.x86_64' // Java for Maven build
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
                script {
                    // Override Java for SonarQube scan to Java 11
                    echo "Setting JAVA_HOME to Java 11 for SonarQube scan"
                    env.JAVA_HOME = "/usr/lib/jvm/java-11-amazon-corretto.x86_64"
                    env.PATH = "${env.JAVA_HOME}/bin:${env.PATH}"
                }
                echo "Running SonarQube scan..."
                sh """
                    mvn sonar:sonar \
                        -Dsonar.projectKey=Java-WebApp-Project \
                        -Dsonar.host.url=http://172.31.88.198:9000 \
                        -Dsonar.login=5a607eb15f33b2508b9ac3390b8109a33da2866d
                """
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
            echo 'Slack Notifications.'
            slackSend(
                channel: '#jenkins-ci-pipeline-alerts-af', // Update with your Slack channel
                color: COLOR_MAP[currentBuild.currentResult],
                message: "*${currentBuild.currentResult}:* Job Name '${env.JOB_NAME}' build ${env.BUILD_NUMBER}\nBuild Timestamp: ${env.BUILD_TIMESTAMP}\nProject Workspace: ${env.WORKSPACE}\nMore info at: ${env.BUILD_URL}"
            )
        }
    }
}
