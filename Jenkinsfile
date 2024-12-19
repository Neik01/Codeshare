pipeline {
    agent any
    environment {
       
        FRONTEND_IMAGE = "codeshare-frontend"
        BACKEND_IMAGE = 'codeshare-backend'
        DOCKER_USERNAME = 'ntkitn'
        PATH = "${env.PATH}:/usr/local/bin"
    }
    tools {
        maven "Maven3.9.9"
        jdk "JDK17"
        nodejs "Node18.20.5"
    }
    stages {
      
        stage('Build Frontend') {
            steps {
                dir('compiler-online-app') {
                    // Install dependencies and build Angular app
                    sh 'npm install'
                    sh 'npm run build --prod'
                }
            }
        }
        stage('Build Backend') {
            steps {
                dir('Backend') {
                  
                    sh 'chmod +x mvnw'
                  
                    sh './mvnw clean package -DskipTests'
                }
            }
        }
        
         stage('Remove Old Docker Images') {
            steps {
                script {
                    sh 'docker images -q ${DOCKER_USERNAME}/${FRONTEND_IMAGE} | xargs -r docker rmi -f'
                    sh 'docker images -q ${DOCKER_USERNAME}/${BACKEND_IMAGE} | xargs -r docker rmi -f'   
                }
            }
        }
      
        stage('Dockerize Application') {
            steps {
               withDockerRegistry([credentialsId:'dockerhub',url:'']){

                sh 'docker build -t ${DOCKER_USERNAME}/${FRONTEND_IMAGE}:latest frontend'
                sh 'docker build -t ${DOCKER_USERNAME}/${BACKEND_IMAGE}:latest Backend'
              
                sh 'docker push ${DOCKER_USERNAME}/${FRONTEND_IMAGE}:latest'
                sh 'docker push ${DOCKER_USERNAME}/${BACKEND_IMAGE}:latest'
               }
            
            }
        }
        stage('Deploy') {
            steps {
                // Deploy to staging/production using Docker or other deployment tools
                echo 'Deploying to environment'
                // Example: Docker Compose for multi-container app
                sh 'docker compose -f docker-compose.yml up -d'
            }
        }
    }
    post {
        always {
            // Cleanup or notify
            echo 'Pipeline finished'
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
        }
    }
}
