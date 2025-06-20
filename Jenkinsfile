pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'e2495a87-3179-47d3-b65a-791c724f92f1' // Use Jenkins credentials for Netlify site ID
        NETLIFY_AUTH_TOKEN = credentials('netlify-token') // Use Jenkins credentials for Netlify auth token    
    }

    stages {
        // stage('Build') {
        //     agent {
        //         docker {
        //             image 'node:18' // Use Node.js 18 Alpine image
        //             reuseNode true // Reuse the same node for this stage
        //         }
        //     }
        //     steps {
        //         sh '''
        //             ls -la
        //             node -v
        //             npm -v
        //             npm ci
        //             npm run build
        //             ls -la
        //         '''
        //     }
        // }

        // stage('Test') {
        //     agent {
        //         docker {
        //             image 'node:18' // Use Node.js 18 Alpine image
        //             reuseNode true // Reuse the same node for this stage
        //         }
        //     }
        //     steps {
        //         sh '''
        //             test -f build/index.html
        //             npm test
        //         '''
        //     }
        // }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18' // Use Node.js 18 Alpine image
                    reuseNode true // Reuse the same node for this stage
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deplouying to production. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
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
