pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
        disableConcurrentBuilds()
    }

    triggers {
        pollSCM('H/1 * * * *')
    }

    environment {
        REGISTRY = 'ayoubsamy/tp4'
        REGISTRY_CREDENTIAL = 'dockerhub-credentials'
    }

    stages {
        stage('Cloning Git') {
            steps {
                git branch: 'main',
                    credentialsId: 'github-credentials',
                    url: 'https://github.com/AyoubSamy/Devops_Cloud_TP4.git'
            }
        }

        stage('Building image') {
            steps {
                script {
                    docker.build(
                        "${env.REGISTRY}:${env.BUILD_NUMBER}"
                    )
                }
            }
        }

        stage('Test image') {
            steps {
                bat '''
                    @echo off
                    docker run --rm "%REGISTRY%:%BUILD_NUMBER%" nginx -t
                    if errorlevel 1 exit /b 1
                '''
            }
        }

        stage('Publish Image') {
            steps {
                script {
                    docker.withRegistry(
                        '',
                        env.REGISTRY_CREDENTIAL
                    ) {
                        def image = docker.image(
                            "${env.REGISTRY}:${env.BUILD_NUMBER}"
                        )

                        image.push()
                    }
                }
            }
        }

        stage('Deploy image') {
            steps {
                bat '''
                    @echo off

                    docker container inspect tp4-web >nul 2>&1
                    if not errorlevel 1 (
                        docker rm -f tp4-web
                        if errorlevel 1 exit /b 1
                    )

                    docker run -d --name tp4-web -p 8081:80 "%REGISTRY%:%BUILD_NUMBER%"
                    if errorlevel 1 exit /b 1
                '''
            }
        }
    }
}