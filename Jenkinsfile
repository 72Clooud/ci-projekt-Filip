pipeline {
    agent any
    stages {
        stage('Info') {
            steps {
                echo "Build: ${env.BUILD_NUMBER}"
                echo "Branch: ${env.BRANCH_NAME}"
            }
        }
        stage('Testy') {
            steps {
                sh 'python3 test_app.py'
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t latest:v1 .'
            }
        }
        stage('Deploy') {
            steps {
                sh '[ ! "$(docker ps -a | grep moja-appka)" ] && docker run -d --name moja-appka latest:v1' 
            }
        }
    }
    post {
        success {
            echo 'SUKCES'
        }
        failure {
            echo 'BLAD!'
        }
    }
}