pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Nidhekshaa/Prodigy_task1.git' // Change this to your repo
            }
        }

        stage('Install Dependencies') {
            parallel {
                stage('Backend Dependencies') {
                    steps {
                        sh 'cd backend && npm install'
                    }
                }
                stage('Frontend Dependencies') {
                    steps {
                        sh 'cd folder && npm install'
                    }
                }
            }
        }

        stage('Lint and Test') {
            parallel {
                stage('Lint Code') {
                    steps {
                        sh 'cd folder && npm run lint'
                    }
                }
                stage('Run Tests') {
                    steps {
                        sh 'cd folder && npm test'
                    }
                }
            }
        }

        stage('Build Frontend') {
            steps {
                sh 'cd folder && npm run build'
            }
        }

        stage('Restart Backend with PM2') {
            steps {
                sh 'cd backend && pm2 restart server.js --name my-backend || pm2 start server.js --name my-backend'
            }
        }

        stage('Deploy Frontend') {
            steps {
                sh 'cp -r folder/build/* /var/www/html/'
            }
        }
    }
}
