pipeline {
    agent any

    environment {
        // 设置 Docker 镜像的标签
        DB_IMAGE = "luluplum/db:latest"
    }

    stages {
        stage('Build DB') {
            steps {
                script {
                    // 构建前端 Docker 镜像
                    bat 'docker build -t luluplum/db:latest ./db'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // 应用 Kubernetes 配置
                    bat 'kubectl apply -f k8s/db-deployment.yaml'
                }
            }
        }

        stage('Service to Kubernetes') {
            steps {
                script {
                    // 应用 Kubernetes 配置
                    bat 'kubectl apply -f k8s/db-service.yaml'
                }
            }
        }

        stage('Service to Kubernetes') {
            steps {
                script {
                    // 应用 Kubernetes 配置
                    bat 'kubectl apply -f k8s/db-hpa.yaml'
                }
            }
        }

    }

    post {
        always {
            // 这里可以添加一些清理步骤，例如清理工作目录或通知
            bat 'docker system prune -f'
        }
        success {
            echo 'Build and deployment succeeded!'
        }
        failure {
            echo 'Build or deployment failed.'
        }
    }
}
