pipeline {
    agent any
    stages {
        stage('拉取数据库') {
            steps {
                git branch: 'sxq', url: 'https://github.com/werwerTrain/database.git'
                echo '拉取成功'
            }
        }
        stage('删除旧容器'){
            steps{
            //删除已有deployment和serve
            bat'''
                kubectl delete -f k8s/db-deployment.yaml
                kubectl delete -f k8s/db-service.yaml
            '''
            // 查找并停止旧的容器
                powershell '''
                $containers = docker ps -q --filter "ancestor=qiuer0121/db:latest"
                foreach ($container in $containers) {
                    Write-Output "Stopping container $container"
                    docker stop $container
                }

                $allContainers = docker ps -a -q --filter "ancestor=qiuer0121/db:latest"
                foreach ($container in $allContainers) {
                    Write-Output "Removing container $container"
                    docker rm $container
                }
                '''
                bat 'docker rmi -f qiuer0121/db:latest || true'
            }
        }
        stage('构建新容器'){
            steps{
                bat 'docker build -t qiuer0121/db ./db'
                echo '构建成功'
            }
        }

        stage('推送远程镜像') {
            steps {
                script {
                        bat '''
                        echo 20050121Rabbit| docker login -u qiuer0121 --password-stdin
                        docker push qiuer0121/db:latest
                        '''
                }
            }
        }
        
        stage('重新部署到k8s'){
            steps{
                bat '''
                kubectl apply -f k8s/db-deployment.yaml
                kubectl apply -f k8s/db-service.yaml
                '''
                echo '部署成功'
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
