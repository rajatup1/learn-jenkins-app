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
                  echo "Deploying to production,Site ID: $NETLIFY_SITE_ID"
                  node_modules/.bin/netlify status


                '''
            }
        }


    }
}
