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

        stage ("Test") {
            agent {
                docker {
                    image "node:18-alpine"
                    reuseNode true
                }
            }

            steps {
                sh '''
                    echo "TEST stage"
                    test -e build/index.html
                    npm test
                '''
            }
        }
    }

    post {
        always {
            junit "test-results/junit.xml"
        }
    }
}