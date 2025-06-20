pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'e2495a87-3179-47d3-b65a-791c724f92f1' // Use Jenkins credentials for Netlify site ID
        }

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine' // Use Node.js 18 Alpine image
                    reuseNode true // Reuse the same node for this stage
                }
            }
            steps {
                sh '''
                    ls -la
                    node -v
                    npm -v
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine' // Use Node.js 18 Alpine image
                    reuseNode true // Reuse the same node for this stage
                }
            }
            steps {
                sh '''
                    test -f build/index.html
                    npm test
                '''
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine' // Use Node.js 18 Alpine image
                    reuseNode true // Reuse the same node for this stage
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "$NETLIFY_SITE_ID"
                '''
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'build/**', allowEmptyArchive: true
            junit 'test-results/junit.xml'
        }
        success {
            echo 'Build and tests completed successfully.'
        }
        failure {
            echo 'Build or tests failed.'
        }
    }
}
