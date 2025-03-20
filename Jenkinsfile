pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
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

      stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                  echo "Test stage"
                  test -f build/index.html
                  npm test
                '''
                echo "Test stage successfully completed"
            }
      }

     stage('E2E Test') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.51.1-noble'
                    reuseNode true
                }
            }
            steps {
                sh '''
                  npm install serve
                  node_modules/.bin/serve -s build
                  npx playwright test
                '''

            }
      }
      
    }

    post {
        always {
            echo 'Publishing JUnit test results...'
            junit 'test-results/junit.xml'
        }
    }
}
