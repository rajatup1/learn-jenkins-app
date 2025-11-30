pipeline {
    agent any
    environment{
        NETLIFY_SITE_ID = '45df71e5-19b7-40de-acc0-079f1fb7f754'
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
        stage("aws deploy"){

          agent{
             docker{
                image 'amazon/aws-cli:latest'
                args '--entrypoint=""'
                reuseNode true
            }
        }
          environment{
            AWS_BUCKET_NAME = 'deno-s3-bucket'
          }

          steps {
                withCredentials([usernamePassword(credentialsId: 'aws-creds', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh'''
                    aws --version
                    aws s3 sync build s3://$AWS_BUCKET_NAME
                    
                    '''
                 }
                
            }
              
        }
        /*
        stage('Test') {
            steps{
                sh '''
                    test -f build/index.html
                    npm test

                '''
            }
         }
         */
        stage('Deploy to staging') {
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
                  echo "Deploying to staging,Site ID: $NETLIFY_SITE_ID"
                  node_modules/.bin/netlify status
                  node_modules/.bin/netlify deploy --dir=build


                '''
            }
        }

         stage('Deploy to production') {
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
                  echo "Deploying to production,Site ID: $NETLIFY_SITE_ID"
                  node_modules/.bin/netlify status
                  node_modules/.bin/netlify deploy --dir=build --prod


                '''
            }
        }


    }
}
