pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Install Frontend Dependencies') {
            steps {
                dir('frontend') {
                    bat '''
                        npm install
                        npm audit fix --force
                    '''
                }
            }
        }

        stage('Lint and Build Frontend') {
            steps {
                dir('frontend') {
                    bat '''
                        npm run lint
                        npm run build
                    '''
                }
            }
        }

        stage('Install Backend Dependencies') {
            steps {
                dir('backend') {
                    bat '''
                        npm install
                        npm audit fix --force
                    '''
                }
            }
        }

        stage('Run Backend Tests') {
            steps {
                dir('backend') {
                    bat '''
                        npm test
                    '''
                }
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonarqube-token')
            }
            steps {
                bat '''
                sonar-scanner -Dsonar.projectKey=merntry1 ^
                              -Dsonar.projectName=merntry1 ^
                              -Dsonar.sources=frontend,backend ^
                              -Dsonar.host.url=http://localhost:9000 ^
                              -Dsonar.token=%SONAR_TOKEN%
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Build or analysis failed. Please check the logs.'
        }
    }
}
