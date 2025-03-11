pipeline {
    agent any


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

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    echo "Test stage"
                    test -f build/index.html
                    npm test
                '''
            }
        }

        stage('AWS') {
            agent {
                docker {
                    image 'amazon/aws-cli'
                    args "--entrypoint ''"
                }
            }

            environment {
                AWS_S3_BUCKET = 'learn-jenkins-202503102034'
            }

            steps {
                withCredentials([usernamePassword(credentialsId: 'my-aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    echo 'AWS stage'
                    sh '''
                        aws --version
                        aws s3 sync buid s3://$AWS_S3_BUCKET
                    '''
                }
            }
        }
    }

    post {
        always {
            junit 'test-results/junit.xml'
        }
    }
}