pipeline {
    agent any

    stages {

        stage('Build db') {
            steps {
                script {
                    // 构建后端 Docker 镜像
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

        stage('Deploy HPA') {
            steps {
                script {
                    // 部署 Horizontal Pod Autoscaler
                    bat 'kubectl apply -f k8s/db-hpa.yaml'
                }
            }
        }

        stage('Integration Test') {
            steps {
                echo 'tested!'
                // 等待应用启动
                //sleep(time: 30, unit: 'SECONDS')
                
                // 使用测试工具进行集成测试
                
                // 使用 Postman Collection 进行测试
                //sh 'newman run collection.json'  // 如果使用 Newman 运行 Postman 测试
                
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