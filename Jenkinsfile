pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Remove existing image if it exists to avoid conflicts
                    sh 'docker rmi -f my-python-app || true'

                    // Build new image
                    sh 'docker build -t my-python-app .'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh '''
                    docker run --rm -v "$PWD:/app" my-python-app \
                    pytest --junitxml=/app/test-results/results.xml --disable-warnings || true
                    '''
                }
            }
        }
    }
    
    post {
        always {
            script {
                if (fileExists('test-results/results.xml')) {
                    junit 'test-results/results.xml'
                } else {
                    echo "No test results found to archive."
                }

                // Clean up dangling images to avoid disk space issues
                sh 'docker image prune -f || true'
            }

            // Archive test result files
            archiveArtifacts artifacts: '**/test-results/*.xml', allowEmptyArchive: true
        }
    }
}
