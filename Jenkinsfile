pipeline {
    agent any

    environment {
        // 设置 Docker 镜像的标签
        DB_IMAGE = "luluplum/db:latest"
        DOCKER_CREDENTIALS_ID = '9b671c50-14d3-407d-9fe7-de0463e569d2'
        DOCKER_PASSWORD = 'luluplum'
        DOCKER_USERNAME = 'woaixuexi0326'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'luluplum', url: 'https://github.com/werwerTrain/database.git'
            }
        }
        stage('Build DB') {
            steps {
                script {
                    // 查找并停止旧的容器
                    sh '''
                    CONTAINERS=$(docker ps -q --filter "ancestor=${DB_IMAGE}")
                    if [ -n "$CONTAINERS" ]; then
                        docker stop $CONTAINERS
                    fi
                    '''
            
                    // 删除停止的容器
                    sh '''
                    CONTAINERS=$(docker ps -a -q --filter "ancestor=${DB_IMAGE}")
                    if [ -n "$CONTAINERS" ]; then
                        docker rm $CONTAINERS
                    fi
                    '''
                    sh '''
                    docker rmi -f ${DB_IMAGE} || true
                    '''
                    // 构建前端 Docker 镜像
                    sh 'docker build -t ${DB_IMAGE} ./db'
                   
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'kubectl delete -f k8s/db-deployment.yaml'
                    // 应用 Kubernetes 配置
                    sh 'kubectl apply -f k8s/db-deployment.yaml'
                }
            }
        }

        stage('Service to Kubernetes') {
            steps {
                script {
                    sh 'kubectl delete -f k8s/db-service.yaml'
                    // 应用 Kubernetes 配置
                    sh 'kubectl apply -f k8s/db-service.yaml'
                }
            }
        }

    }

    post {
        always {
            // 这里可以添加一些清理步骤，例如清理工作目录或通知
            sh 'docker system prune -f'
        }
        success {
            echo 'Build and deployment succeeded!'
        }
        failure {
            echo 'Build or deployment failed.'
        }
    }
}
