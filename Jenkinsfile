pipeline {
    agent any

    stages {
        // stage('Build') {
        //     agent {
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //             ls -la
        //             node --version
        //             npm --version
        //             npm ci
        //             npm run build
        //             ls -la
        //         '''
        //     }
        // }

      // stage ('Test') {
      //   parallel {
      //       stage('Unit Tests') {
      //                 agent {
      //                     docker {
      //                         image 'node:18-alpine'
      //                         reuseNode true
      //                     }
      //                 }
      //                 steps {
      //                     sh '''
      //                       echo "Test stage"
      //                       test -f build/index.html
      //                       npm test
      //                     '''
      //                     echo "Test stage successfully completed"
      //                 }

      //             post {
      //                 always {
      //                     echo 'Publishing JUnit test results...'
      //                     junit 'jest-results/junit.xml'
      //                 }
      //             }
      //           }

      //         stage('E2E Test') {
      //                 agent {
      //                     docker {
      //                         image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
      //                         reuseNode true
      //                     }
      //                 }
      //                 steps {
      //                     sh '''
      //                       npm install serve
      //                       node_modules/.bin/serve -s build &
      //                       sleep 10
      //                       npx playwright test --reporter=line
      //                     '''

      //                 }
      //             post {
      //                 always {
      //                   publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
      //                 }
      //             }
      //           }
      //   }
      // }      

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
                  node_module/.bin/netlify --version
                '''
            }
        }
    }
}
