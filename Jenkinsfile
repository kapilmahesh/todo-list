pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/kapilmahesh/todo-list.git', branch: 'main'
            }
        }
        stage('Build and Run') {
            steps {
                sh 'docker-compose -p ci-project -f docker-compose-ci.yml up --build -d'
            }
        }
        stage('Test') {
            steps {
                sh 'sleep 5 && curl -f http://localhost:6000/api/tasks'
            }
        }
    }
    post {
        always {
            sh 'docker-compose -p ci-project -f docker-compose-ci.yml down || true'
        }
    }
}