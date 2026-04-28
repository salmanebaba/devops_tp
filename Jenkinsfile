pipeline {
    agent any // This defines where the pipeline runs (any available runner/node)
    tools {
        // This must match the name configured in Manage Jenkins -> Global Tool Configuration
        maven 'Maven 3.9.15' 
        jdk 'Java 21.0.11'
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
                    // This gathers the test results so you can see them in the Jenkins UI
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Package') {
            steps {
                // This creates the .jar file in the target/ folder
                sh 'mvn package'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                echo 'Deploying to Tomcat Server...'
                
                // Method 1: Using the 'Deploy to container' plugin
                // 'contextPath' is the URL suffix (e.g., http://localhost:8080/myapp)
                deploy snippets: [
                    containerAdapter: tomcat9(
                        credentialsId: 'tomcat-credentials', // Created in Jenkins Credentials
                        url: 'http://0.0.0.0:8081'
                    ),
                    contextPath: 'my-devops-app',
                    war: 'target/*.war'
                ]
                
                /* Method 2: If you don't want plugins, use a simple script (Manager App must be enabled):
                sh "curl -u admin:password --upload-file target/*.war 'http://localhost:8080/manager/text/deploy?path=/my-devops-app&update=true'"
                */
            }
        }
    }

    post {
        success {
            echo 'Pipeline finished successfully!'
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true // This saves the .jar file as a build artifact
        }
        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}
