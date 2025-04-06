pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '267e9ee4-acef-4116-af79-e0e33763e2dd'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        REACT_APP_VERSION = "0.1.$BUILD_ID"
        AWS_DEFAULT_REGION = 'ap-south-1'
    }

    stages {

        stage('Deploy to aws fargate') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "--entrypoint=''"
                }
            }

            environment {
                AWS_S3_BUCKET = 'my-jenkins-learning-app-bucket'
                AWS_TASK_DEFINITION_PATH = 'aws/task-definition.json'
            }

            steps {
                withCredentials([usernamePassword(credentialsId: 'jenkins-accessKey', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                        aws --version
                        aws ecs register-task-definition --cli-input-json file://$AWS_TASK_DEFINITION_PATH
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
    }
}
