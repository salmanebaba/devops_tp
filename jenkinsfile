pipeline {
    agent any // This defines where the pipeline runs (any available runner/node)

    stages {
        stage('Build') {
            steps {
                echo 'Compiling the code...'
                echo 'Compile complete.'
            }
        }

        stage('Test') {
            // Jenkins runs steps sequentially by default. 
            // To mimic GitLab's parallel jobs, we use a 'parallel' block.
            parallel {
                stage('Unit Test') {
                    steps {
                        echo 'Running unit tests... This will take about 60 seconds.'
                        sleep 60
                        echo 'Code coverage is 90%'
                    }
                }
                stage('Lint Test') {
                    steps {
                        echo 'Linting code... This will take about 10 seconds.'
                        sleep 10
                        echo 'No lint issues found.'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                // 'environment' in GitLab is handled here via logic or plugins
                echo 'Deploying application to production...'
                echo 'Application successfully deployed.'
            }
        }
    }

    post {
        success {
            echo 'Pipeline finished successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the logs.'
        }
    }
}
