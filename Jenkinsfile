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
                    // 构建数据库 Docker 镜像
                    bat 'docker build -t ${DB_IMAGE} ./db'
                }
            }
        }

        stage('Push DB Image') {
            steps {
                script {
                    // 推送 Docker 镜像到 Docker Registry
                    bat 'docker push ${DB_IMAGE}'
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
