pipeline {
    agent any
    options {
        timeout(time: 20, unit: 'MINUTES')
    }
    environment {
            APP_NAME = 'MojaAplikacja'
            APP_VERSION = '1.0.0'
    }
    parameters {
        choice(
            name: 'SRODOWISKO',
            choices: ['dev', 'staging', 'prod'],
            description: 'Wybierz srodowisko docelowe'
        )
    }
    stages {
        stage('Info') {
            steps {
                echo "Nazwa: ${env.APP_NAME}"
                echo "Wersja: ${env.APP_VERSION}"
                echo "Build: ${env.BUILD_NUMBER}"
                echo "Branch: ${env.BRANCH_NAME}"
                echo "Srodowisko: ${params.SRODOWISKO}"
            }
        }
        stage('Testy') {
            when {
                expression {
                    env.GIT_BRANCH != 'origin/main'
                }
            }
            steps {
                sh 'python3 test_app.py'
            }
        }
        stage('Build') {
            steps {
                sh "docker build -t moja-appka:${env.BUILD_NUMBER} ."
            }
        }
        stage('Analiza jakosci') {
            parallel {
                stage('Sprawdzenie plikow') {
                    steps {
                        sh 'ls app.py test_app.py tools.py Dockerfile'
                    }
                }
                stage('Skanowanie obrazu') {
                    steps {
                        sh "docker inspect moja-appka:${env.BUILD_NUMBER}"
                    }
                }
            }
        }
        stage('Wdrożenie DEV') {
            when {
                expression { params.SRODOWISKO == 'dev' }
            }
            steps {
                echo 'Wdrażanie na środowisko deweloperskie'
            }
        }
        stage('Zatwierdzenie') {
            when {
                expression { params.SRODOWISKO == 'prod' }
            }
            options {
                timeout(time: 5, unit: 'MINUTES')
            }
            steps {
                input message: 'Czy chcesz wdrażać na produkcję?', ok: 'Tak, wdrazaj!'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker stop moja-appka || true'
                sh 'docker rm moja-appka || true'
                sh "docker run -d --name moja-appka -p 5000:5000 moja-appka:${env.BUILD_NUMBER}"
            }
        }
    }
    post {
        success {
            echo "Sukces na środowisku: ${params.SRODOWISKO}, Build: ${env.BUILD_NUMBER}"
        }
        failure {
            echo 'BŁĄD: Pipeline zakończony niepowodzeniem.'
        }
        always {
            echo 'Zakończono wykonywanie Pipeline.'
        }
    }
}
