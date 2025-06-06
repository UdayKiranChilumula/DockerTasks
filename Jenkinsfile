pipeline {
    agent any

    parameters {
        string(name: 'REMOTE_HOST', defaultValue: '35.171.26.232', description: 'Public IP of the EC2 instance')
        string(name: 'APP_FOLDER_PATH', defaultValue: 'Docker-Tasks/Dockerfile-Assignment/use_case-1', description: 'Relative path to the app folder on EC2')
        string(name: 'DOCKER_IMAGE', defaultValue: 'flask-hello-app', description: 'Docker image name to build')
        string(name: 'CONTAINER_NAME', defaultValue: 'flask-hello-container', description: 'Docker container name to run')
        string(name: 'EXPOSE_PORT_SER', defaultValue: '5000', description: 'Port to expose on EC2 host')
        string(name: 'EXPOSE_PORT_CON', defaultValue: '5000', description: 'Port to expose on container')
    }

    stages {
        stage('Build and Run on EC2') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'ssh-private-key-ec2', keyFileVariable: 'SSH_KEY')]) {
                    sh """
                        ssh -i $SSH_KEY -o StrictHostKeyChecking=no ec2-user@${params.REMOTE_HOST} '
                            cd ${params.APP_FOLDER_PATH}

                            echo "[+] Building Docker Image: ${params.DOCKER_IMAGE}..."
                            docker build -t ${params.DOCKER_IMAGE} .

                            echo "[+] Removing existing container: ${params.CONTAINER_NAME} (if exists)..."
                            docker rm -f ${params.CONTAINER_NAME} || true

                            echo "[+] Running Container on Port ${params.EXPOSE_PORT}..."
                            docker run -d --name ${params.CONTAINER_NAME} -p ${params.EXPOSE_PORT}:${params.EXPOSE_PORT} ${params.DOCKER_IMAGE}
                        '
                    """
                }
            }
        }
    }
}
