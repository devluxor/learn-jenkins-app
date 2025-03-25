pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '301b95ca-3e2e-4469-8426-54dfd977273c'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

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

              // stage('E2E Test') {
              //         agent {
              //             docker {
              //                 image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
              //                 reuseNode true
              //             }
              //         }
              //         steps {
              //             sh '''
              //               npm install serve
              //               node_modules/.bin/serve -s build &
              //               sleep 10
              //               npx playwright test --reporter=line
              //             '''

              //         }
              //     post {
              //         always {
              //           publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Local Report', reportTitles: '', useWrapperFileDirectly: true])
              //         }
              //     }
              //   }
        // }
      // }      

       stage('Deploy staging') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                  npm install netlify-cli node-jq
                  node_modules/.bin/netlify --version
                  echo "Deploying to Netlify for staging... Site ID: $NETLIFY_SITE_ID"
                  node_modules/.bin/netlify status
                  node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json
                '''
            }

            script {
              env.STAGING_URL = sh(script: "node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json", returnStdout: true)
            }
        }

      stage('Staging E2E Test') {
          agent {
              docker {
                  image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                  reuseNode true
              }
          }

          environment {
            CI_ENVIRONMENT_URL = "$env.STAGING_URL"
          }   

          steps {
              sh '''
                npx playwright test --reporter=html
              '''
          }
          post {
              always {
                  publishHTML([
                    allowMissing: false, 
                    alwaysLinkToLastBuild: false, 
                    icon: '', 
                    keepAll: false, 
                    reportDir: 'playwright-report', 
                    reportFiles: 'index.html', 
                    reportName: 'Playwright E2E Staging Report', 
                    reportTitles: '', 
                    useWrapperFileDirectly: true
                  ])
              }
          }
      }        

        stage('Approval') {
            steps {
                timeout(time: 15, unit: 'MINUTES') {
                    input message: 'Ready to deploy for production?', ok: 'Yes, I\'m ready'
                }
            }
        }

       stage('Deploy production') {
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
                  echo "Deploying to Netlify for production... Site ID: $NETLIFY_SITE_ID"
                  node_modules/.bin/netlify status
                  node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }

      // stage('Prod E2E Test') {
      //     agent {
      //         docker {
      //             image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
      //             reuseNode true
      //         }
      //     }

      //     environment {
      //       CI_ENVIRONMENT_URL = 'https://startling-rabanadas-2b55d4.netlify.app'
      //     }   

      //     steps {
      //         sh '''
      //           npx playwright test --reporter=html
      //         '''
      //     }
      //     post {
      //         always {
      //             publishHTML([
      //               allowMissing: false, 
      //               alwaysLinkToLastBuild: false, 
      //               icon: '', 
      //               keepAll: false, 
      //               reportDir: 'playwright-report', 
      //               reportFiles: 'index.html', 
      //               reportName: 'Playwright E2E Report', 
      //               reportTitles: '', 
      //               useWrapperFileDirectly: true
      //             ])
      //         }
      //     }
      // }
    }
}
