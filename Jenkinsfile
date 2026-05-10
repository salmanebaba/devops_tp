pipeline {
    agent any 
    tools {
        jdk 'Java 21.0.11'
        maven 'Maven 3.9.12'
    }

    stages {
        
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
                sh '''
                    fuser -k 8081/tcp || true
                    JAR=$(ls target/*.jar | grep -v original | head -n 1)
                    echo "Running $JAR"
                    nohup java -jar "$JAR" --server.port=8081 > app.log 2>&1 &
                '''
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