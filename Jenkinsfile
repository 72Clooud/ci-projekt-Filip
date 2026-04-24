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
                sh 'docker build -t latest:v1 .'
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker stop moja-appka || true'
                sh 'docker rm moja-appka || true'
                sh 'docker run -d --name moja-appka -p 5000:5000 latest:v1'
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
