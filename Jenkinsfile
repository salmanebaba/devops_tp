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

        stage('Deploy to Tomcat') {
            steps {
                echo 'Deploying to Tomcat Server...'
                
                deploy(
                    adapters: [tomcat9(
                        credentialsId: 'tomcat-credentials', 
                        url: 'http://localhost:8081'
                    )],
                    contextPath: 'devops_tp',
                    war: '**/target/*.war'
                )
            }
        }
    }

    post {
        success {
            echo 'Pipeline finished successfully!'
            archiveArtifacts artifacts: 'target/*.war', fingerprint: true
        }
        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}