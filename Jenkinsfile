pipeline {
    agent any

    stages {
        stage('Build Backend Image') {
            steps {
                sh 'docker build -t backend-app backend'
            }
        }

        stage('Deploy Backends') {
            steps {
                sh 'docker rm -f backend1 backend2 || true'
                sh 'docker run -d --name backend1 backend-app'
                sh 'docker run -d --name backend2 backend-app'
                sh 'sleep 3'
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                // 1. Clean up old container
                sh 'docker rm -f nginx-lb || true'
                
                // 2. Start NGINX (Plain, no config yet)
                sh 'docker run -d --name nginx-lb -p 80:80 nginx'
                
                // 3. Wait for it to initialize
                sh 'sleep 3'
                
                // 4. COPY the config files from Jenkins to the NGINX container
                // This fixes the mounting error!
                sh 'docker cp nginx/nginx.conf nginx-lb:/etc/nginx/nginx.conf'
                sh 'docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf'
                
                // 5. Reload NGINX to apply the new config
                sh 'docker exec nginx-lb nginx -s reload'
            }
        }

        stage('Post Actions') {
            steps {
                echo 'Pipeline executed successfully, NGINX load balancer is running.'
            }
        }
    }
}
