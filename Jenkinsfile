pipeline {
    agent any

    environment {
        // NETLIFY_SITE_ID = '301b95ca-3e2e-4469-8426-54dfd977273c'
        // NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        REACT_APP_VERSION = "1.2.$BUILD_ID"
    }


    stages {

        stage('Deploy to AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "--entrypoint=''"
                }
            }

            environment {
              // AWS_S3_BUCKET = 'learn-jenkins-123456'
              AWS_DEFAULT_REGION = 'eu-west-1'
            }

            steps {
                withCredentials([usernamePassword(credentialsId: 'aws-access-key', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                      aws --version
                      aws ecs register-task-definition --cli-input-json file://aws/task-definition-prod.json
     
                      aws ecs update-service --cluster learn-jenkins-app-cluster-prod --service LearnJenkinsApp-Service-Prod --task-definition LearnJenkinsApp-TaskDefinition-Prod:2
                    '''
                }
            }
        }

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

      // stage('Deploy staging') {
      //       agent {
      //           docker {
      //               image 'node:18-alpine'
      //               reuseNode true
      //           }
      //       }
      //       steps {
      //           sh '''
      //             npm install netlify-cli node-jq
      //             node_modules/.bin/netlify --version
      //             echo "Deploying to Netlify for staging... Site ID: $NETLIFY_SITE_ID"
      //             node_modules/.bin/netlify status
      //             node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json
      //           '''
      //           script {
      //             env.STAGING_URL = sh(script: "node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json", returnStdout: true)
      //           }
      //       }
      //   }

      // stage('Deploy to staging + E2E Test') {
      //     agent {
      //         docker {
      //             // image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
      //             image 'my-playwright'
      //             reuseNode true
      //         }
      //     }

      //     environment {
      //       CI_ENVIRONMENT_URL = 'STAGING_URL_TO_BE_SET'
      //     }   

      //     steps {
      //         sh '''
      //           npm install netlify-cli node-jq
      //           netlify --version
      //           echo "Deploying to Netlify for staging... Site ID: $NETLIFY_SITE_ID"
      //           netlify status
      //           netlify deploy --dir=build --json > deploy-output.json
      //           CI_ENVIRONMENT_URL=$(node-jq -r '.deploy_url' deploy-output.json)
                
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
      //               reportName: 'Playwright E2E Staging Report', 
      //               reportTitles: '', 
      //               useWrapperFileDirectly: true
      //             ])
      //         }
      //     }
      // }        

        // stage('Approval') {
        //     steps {
        //         timeout(time: 15, unit: 'MINUTES') {
        //             input message: 'Ready to deploy for production?', ok: 'Yes, I\'m ready'
        //         }
        //     }
        // }

      //  stage('Deploy production') {
      //       agent {
      //           docker {
      //               image 'node:18-alpine'
      //               reuseNode true
      //           }
      //       }
      //       steps {
      //           sh '''
      //             npm install netlify-cli
      //             node_modules/.bin/netlify --version
      //             echo "Deploying to Netlify for production... Site ID: $NETLIFY_SITE_ID"
      //             node_modules/.bin/netlify status
      //             node_modules/.bin/netlify deploy --dir=build --prod
      //           '''
      //       }
      //   }

      // stage('Deploy to prod + E2E Test') {
      //     agent {
      //         docker {
      //             // image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
      //             image 'my-playwright'
      //             reuseNode true
      //         }
      //     }

      //     environment {
      //       CI_ENVIRONMENT_URL = 'https://startling-rabanadas-2b55d4.netlify.app'
      //     }   

      //     steps {
      //         sh '''
      //           netlify --version
      //           echo "Deploying to Netlify for production... Site ID: $NETLIFY_SITE_ID"
      //           netlify status
      //           netlify deploy --dir=build --prod

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
