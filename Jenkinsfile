pipeline {
    agent any

    stages {
        agent {
            docker {
                image "node:18-alpine"
                reuseNode true
            }
        }
        stage('Build') {
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

        stage('Test') {
            agent {
                docker {
                    image "node:18-alpine"
                    reuseNode true
                }
            }
            steps {
                sh '''
                    if test -f build/index.html; then
                        echo "index.html file exists."
                    else
                        exit 1
                    fi

                    npm test
                '''
            }
        }

        stage('E2E') {
            agent {
                docker {
                    image "mcr.microsoft.com/playwright:v1.46.0-jammy"
                    reuseNode true
                }
            }
            steps {
                sh '''
                    npm install serve
                    node_modules/.bin/serve -s build &
                    sleep 10
                    npx playwright test
                '''
            }
        }
    }

    post {
        always {
            junit 'jest-results/junit.xml'
        }
    }
}
