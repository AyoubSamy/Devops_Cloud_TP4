pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
        disableConcurrentBuilds()
    }

    triggers {
        pollSCM('* * * * *')
    }

    environment {
        REGISTRY = 'ayoubsamy/tp4'
        REGISTRY_CREDENTIAL = 'dockerhub-credentials'
    }

    stages {
        stage('Cloning Git') {
            steps {
                checkout scm
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
                    docker run --rm %REGISTRY%:%BUILD_NUMBER% nginx -t
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
    }
}