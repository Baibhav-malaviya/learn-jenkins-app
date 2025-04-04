pipeline {
    agent any

    environment {
        NETLIFY_AUTH_TOKEN = credentials("netlify-token")
        NETLIFY_SITE_ID = '267e9ee4-acef-4116-af79-e0e33763e2dd'
    }

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

        stage ("TEST") {
            parallel {
                stage ("unit test") {
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

                    post {
                        always {
                            junit "test-results/junit.xml"
                        }
                    }
                }

                /*
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

                            npx playwright test
                        '''
                    }
                }
                */
            }
        }

        stage ("Deploy") {
            agent {
                docker {
                    image "node:18-alpine"
                    reuseNode true
                }
            }

            steps {
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploying to netlify. SITE_ID $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }

    }
}