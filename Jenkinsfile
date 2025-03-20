pipeline {
    agent any

    environment {
        NODE_VERSION = '18'   // Specify Node.js version
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Nidhekshaa/Prodigy_task1' // Change to your repo URL
            }
        }

        stage('Install Dependencies') {
            parallel {
                stage('Install Backend Dependencies') {
                    steps {
                        dir('backend') {
                            sh 'npm install'
                        }
                    }
                }
                stage('Install Frontend Dependencies') {
                    steps {
                        dir('folder') {
                            sh 'npm install'
                        }
                    }
                }
            }
        }

        stage('Lint & Prettier') {
            parallel {
                stage('Lint Backend') {
                    steps {
                        dir('backend') {
                            sh 'npm run lint'
                        }
                    }
                }
                stage('Lint Frontend') {
                    steps {
                        dir('frontend') {
                            sh 'npm run lint'
                        }
                    }
                }
            }
        }

        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm run build'
                }
            }
        }

        stage('Run Tests') {
            parallel {
                stage('Test Backend') {
                    steps {
                        dir('backend') {
                            sh 'npm test'
                        }
                    }
                }
                stage('Test Frontend') {
                    steps {
                        dir('frontend') {
                            sh 'npm test'
                        }
                    }
                }
            }
        }

        stage('Deploy Backend') {
            steps {
                dir('backend') {
                    sh 'pm2 restart server.js'  // Using PM2 for backend
                }
            }
        }

        stage('Deploy Frontend') {
            steps {
                dir('frontend') {
                    sh 'cp -r build/* /var/www/html/'  // Change the path as needed
                }
            }
        }

        stage('Restart Server') {
            steps {
                sh 'pm2 restart all'
            }
        }
    }

    post {
        success {
            echo 'Deployment Successful!'
        }
        failure {
            echo 'Deployment Failed!'
        }
    }
}
