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
        stage('Initialize Database') {
            steps {
                sh 'sleep 10 && docker exec ci-project_ci-mysql_1 mysql -uroot -ppassword todo_db -e "CREATE TABLE IF NOT EXISTS tasks (id INT AUTO_INCREMENT PRIMARY KEY, task VARCHAR(255) CHARACTER SET utf8mb4)"'
            }
        }
        stage('Test') {
            steps {
                sh 'sleep 5 && curl -X POST http://localhost:6000/api/tasks -H "Content-Type: application/json" -d "{\"task\":\"CI Test\"}"'
                sh 'curl -f http://localhost:6000/api/tasks'
            }
        }
    }
    post {
        always {
            sh 'docker-compose -p ci-project -f docker-compose-ci.yml down || true'
        }
    }
}