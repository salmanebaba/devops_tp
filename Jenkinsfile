pipeline {
    agent any 
    tools {
        maven 'Maven 3.9.12' 
        jdk 'Java 21.0.11'
    }

    stages {
        stage('Initialize Environment') {
            steps {
                script {
                    // Pre-loading tool paths to ensure 'mvn' and 'java' use Version 21 
                    def mvnHome = tool 'Maven 3.9.12'
                    def jdkHome = tool 'Java 21.0.11'
                    env.PATH = "${mvnHome}/bin:${jdkHome}/bin:${env.PATH}"
                    env.JAVA_HOME = "${jdkHome}"
                }
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Unit Tests') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('Deploy (Run JAR)') {
            steps {
                echo 'Starting the Spring Boot Application...'
                // Since you are using JAR, we run it directly. 
                // We kill any existing process on 8081 first to avoid port conflicts.
                sh '''
                    fuser -k 8081/tcp || true
                    nohup java -jar target/*.jar --server.port=8081 > app.log 2>&1 &
                '''
                echo 'Application is running on http://localhost:8081'
            }
        }
    }

    post {
        success {
            echo 'Pipeline finished successfully!'
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}