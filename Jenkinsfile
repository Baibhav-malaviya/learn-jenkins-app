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

        stage ("E2E") {
            agent {
                docker {
                    image "mcr.microsoft.com/playwright:v1.51.1-noble"
                    reuseNode true
                }
            }

            steps {
                sh '''
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 5

                    npx playwright install --with-deps
                    npx playwright test
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