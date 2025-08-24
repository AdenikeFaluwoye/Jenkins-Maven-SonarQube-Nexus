pipeline {
    agent any

    environment {
        // System paths
        MAVEN_HOME = '/opt/maven'
        JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto'
        PATH = "${MAVEN_HOME}/bin:${JAVA_HOME}/bin:${env.PATH}"

        // Credentials from Jenkins
        NEXUS_HOST = credentials('NEXUS_HOST')
        NEXUS_USER = credentials('NEXUS_USER')
        NEXUS_PASS = credentials('NEXUS_PASS')
        SONAR_TOKEN = credentials('SONAR_TOKEN')
        GITHUB_CREDENTIALS = 'GITHUB_CREDENTIALS'   // ID of your GitHub PAT credential
        SLACK_WEBHOOK = credentials('SLACK_WEBHOOK')
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out code..."
                git branch: 'main', 
                    url: 'https://github.com/AdenikeFaluwoye/Jenkins-Maven-SonarQube-Nexus.git', 
                    credentialsId: "${GITHUB_CREDENTIALS}"
            }
        }

        stage('Validate Project') {
            steps {
                echo "Validating project..."
                sh 'mvn validate'
            }
        }

        stage('Build') {
            steps {
                echo "Building project..."
                sh 'mvn -v'   // verify Maven & Java
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_HOST_URL = 'http://<SonarQube-private-IP>:9000'  // replace with your private IP
            }
            steps {
                echo "Running SonarQube analysis..."
                withSonarQubeEnv('sonar') {
                    sh 'mvn sonar:sonar -Dsonar.token=$SONAR_TOKEN -Dsonar.host.url=$SONAR_HOST_URL'
                }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                echo "Deploying artifact to Nexus..."
                sh 'mvn deploy -DskipTests'
            }
        }
    }

    post {
        success {
            echo " Build & deployment successful!"
            withCredentials([string(credentialsId: 'SLACK_WEBHOOK', variable: 'WEBHOOK')]) {
                sh """
                curl -X POST -H 'Content-type: application/json' \
                --data '{"text":" Jenkins Build Successful: ${JOB_NAME} #${BUILD_NUMBER}"}' \
                $WEBHOOK
                """
            }
        }
        failure {
            echo " Build failed!"
            withCredentials([string(credentialsId: 'SLACK_WEBHOOK', variable: 'WEBHOOK')]) {
                sh """
                curl -X POST -H 'Content-type: application/json' \
                --data '{"text":" Jenkins Build Failed: ${JOB_NAME} #${BUILD_NUMBER}"}' \
                $WEBHOOK
                """
            }
        }
    }
}
