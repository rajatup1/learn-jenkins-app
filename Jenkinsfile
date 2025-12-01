pipeline {
    agent any
    environment{
        REACT_APP_VERSION = "1.0.$BUILD_ID"
        AWS_DEFAULT_REGION = "us-east-1"
        
    }

    stages {
         stage("aws deploy"){

          agent{
             docker{
                image 'amazon/aws-cli:latest'
                args '--entrypoint=""'
                reuseNode true
            }
        }
    
          steps {
                withCredentials([usernamePassword(credentialsId: 'aws-creds', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh'''
                    aws --version
                    aws ecs register-task-definition --cli-input-json file://aws/task-definition-prod.json
                    aws ecs update-service --cluster learn-jenkins-prod  --service learn-jenkins-prod-taskdefinition-service-lf4jjyoa  --task-definition learn-jenkins-prod-taskdefinition:2
                    '''
                 }
                
            }
              
        }
        stage('Build') {
            agent {
                docker {
                    image 'node:18'
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

    }
}
