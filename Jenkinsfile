pipeline {
    agent any
    environment {
        DOCKER_HUB_CRED = credentials('docker-hub-cred')
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/kapilmahesh/todo-list.git', branch: 'main'
            }
        }
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t yourusername/todo-crud-app:latest .'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                sh 'echo $DOCKER_HUB_CRED_PSW | docker login -u $DOCKER_HUB_CRED_USR --password-stdin'
                sh 'docker push yourusername/todo-crud-app:latest'
            }
        }
        stage('Run Containers') {
            steps {
                sh 'docker-compose -p ci-project -f docker-compose-ci.yml up --build -d'
                sh 'sleep 10' // Wait for containers to initialize
            }
        }
        stage('Initialize Database') {
            steps {
                sh '''
                docker exec ci-project_ci-mysql_1 mysql -uroot -ppassword -e "CREATE DATABASE IF NOT EXISTS todo_db;"
                docker exec ci-project_ci-mysql_1 mysql -uroot -ppassword todo_db -e "CREATE TABLE IF NOT EXISTS tasks (id INT AUTO_INCREMENT PRIMARY KEY, task VARCHAR(255) CHARACTER SET utf8mb4);"
                '''
            }
        }
        stage('Test API') {
            steps {
                sh 'curl -f http://localhost:6000/api/tasks || exit 1'
                sh '''
                curl -X POST http://localhost:6000/api/tasks -H "Content-Type: application/json" -d '{"task":"CI Test"}' || exit 1
                curl -f http://localhost:6000/api/tasks | grep "CI Test" || exit 1
                '''
            }
        }
    }
    post {
        always {
            sh 'docker-compose -p ci-project -f docker-compose-ci.yml down -v || true'
            sh 'docker system prune -f || true'
        }
        failure {
            echo 'Pipeline failed. Check logs for details.'
        }
    }
}