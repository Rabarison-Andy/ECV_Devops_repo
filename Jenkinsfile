pipeline {
    agent any

    options {
        disableConcurrentBuilds() //Interdit de lancer ce job 2 fois en même temps
        parallelsAlwaysFailFast() //Dans un parallel, si l'un des threads échoue, stoppe les autres
    }

    environment {
        IMAGE_NAME = "task_api"
    }

    stages {
        stage('Install') {
            steps {
                sh 'npm ci'
            }
        }
        stage('Qualité') {
            parallel {
                stage('Lint') {
                    steps {
                        sh 'npm run lint'
                    }
                }
                stage('Tests') {
                    steps {
                        sh 'npm run test:coverage'
                    }
                }
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} .
                docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest
                '''
            }
        }


        stage('Deploy') {
            steps {
                sh 'docker run -d -p 3002:3000 ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
    }
}