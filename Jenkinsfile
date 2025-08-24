pipeline {
    agent any

    environment {
        // System paths
        MAVEN_HOME = '/opt/maven'
        JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto'
        PATH+EXTRA = "${MAVEN_HOME}/bin:${JAVA_HOME}/bin"

        // Credentials from Jenkins
        NEXUS_HOST = credentials('NEXUS_HOST')
        NEXUS_USER = credentials('NEXUS_USER')
        NEXUS_PASS = credentials('NEXUS_PASS')
        SONAR_TOKEN = credentials('SONAR_TOKEN')
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your/repo.git'
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
                JAVA_HOME = '/usr/lib/jvm/java-11-amazon-corretto'     // override for Sonar if needed
                PATH+EXTRA = "${JAVA_HOME}/bin:${env.PATH}"            // ensure Java 11 is used
            }
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'java -version'   // confirm correct Java version
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
        }
        failure {
            echo " Build failed!"
        }
    }
}
