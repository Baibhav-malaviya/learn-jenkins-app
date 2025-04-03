pipeline {
    agent any

    stages {
        stage ("Hello") {
            steps {
                echo "Hello world!"
            }
        }

        stage ("Build") {
            agent {
                docker {
                    image "node:18-alpine"
                    reuseNode true
                }
            }

            steps {
                sh '''
                    ls -a
                    node --version
                    npm --version
                    npm ci
                    npm run build
                    ls -a
                '''
            }
        }
    }
}