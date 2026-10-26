pipeline {
    agent {
        label 'agent-windows'
    }

    environment {
        DOCKERHUB_USER = "siradia1"
        BACKEND_IMAGE  = "backend-employe"
        FRONTEND_IMAGE = "frontend-employe"
        IMAGE_TAG      = "1.${BUILD_NUMBER}"
    }

    stages {
        stage('Build Backend Image') {
            steps {
                bat 'docker build -t %DOCKERHUB_USER%/%BACKEND_IMAGE%:%IMAGE_TAG% ./backend'
            }
        }

        stage('Build Frontend Image') {
            steps {
                bat 'docker build -t %DOCKERHUB_USER%/%FRONTEND_IMAGE%:%IMAGE_TAG% ./frontend'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat '''
                        @echo off
                        echo %DOCKER_PASS%| docker login --username %DOCKER_USER% --password-stdin
                        if errorlevel 1 exit /b 1

                        docker push %DOCKERHUB_USER%/%BACKEND_IMAGE%:%IMAGE_TAG%
                        if errorlevel 1 exit /b 1

                        docker push %DOCKERHUB_USER%/%FRONTEND_IMAGE%:%IMAGE_TAG%
                        if errorlevel 1 exit /b 1
                    '''
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                bat 'docker compose -p gestion-employe up -d'
            }
        }
    }

    post {
        success {
            echo 'Build et publication des images réussis !'
        }
        failure {
            echo 'Échec du pipeline : vérifiez les logs Jenkins.'
        }
    }
}