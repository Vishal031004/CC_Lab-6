pipeline {
    agent any

    stages {
        stage('Build Backend Image') {
            steps {
                // Build the docker image from the current directory
                sh 'docker build -t backend-app backend'
            }
        }

        stage('Deploy Backends') {
            steps {
                // Remove existing containers if they exist
                sh 'docker rm -f backend1 backend2 || true'
                
                // Deploy two backend containers
                sh 'docker run -d --name backend1 backend-app'
                sh 'docker run -d --name backend2 backend-app'
                
                // Wait for containers to fully start (Fix from manual)
                sh 'sleep 3'
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                // Remove existing nginx container
                sh 'docker rm -f nginx-lb || true'
                
                // Start NGINX. We mount the config file from the current workspace
                // Since your files are in the root, we use $(pwd)/nginx/...
                sh '''
                    docker run -d --name nginx-lb -p 80:80 \
                    -v $(pwd)/nginx/nginx.conf:/etc/nginx/nginx.conf \
                    -v $(pwd)/nginx/default.conf:/etc/nginx/conf.d/default.conf \
                    nginx
                '''
                
                // Wait for NGINX to initialize
                sh 'sleep 2'
            }
        }

        stage('Post Actions') {
            steps {
                echo 'Pipeline executed successfully, NGINX load balancer is running.'
            }
        }
    }
}
