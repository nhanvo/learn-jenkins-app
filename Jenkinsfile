pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -la
                '''
            }
        }
        stage('Tests') {
            parallel {
                  stage('Unit Test') {
                      steps {
                        sh '''
                            echo "Test stage"
                            npm test
                        '''
                        }
                        post {
                            always {
                                junit 'test-results/junit.xml'                                
                            }
                        }
                    }
                    stage('E2E') {
                        steps {
                            sh '''                   
                                npm install -g serve
                                serve -s build &
                                sleep 10
                                npx playwright install
                                npx playwright test --reporter=html
                            '''
                        }
                        post {
                            always {
                                publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                            }
                        }
                    }
            }
        }
        // Deployment stage
        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                '''
            }
        }
    }
}