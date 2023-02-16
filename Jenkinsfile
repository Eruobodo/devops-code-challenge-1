pipeline {
    agent any
  
    environment {
	    AWS_DEFAULT_REGION="us-east-2"
	    THE_BUTLER_SAYS_SO=credentials('aws-cred')
      }

    stages {
        stage('Checkout') {
              steps {
                  git url: 'https://github.com/Eruobodo/devops-code-challenge-1.git'
              }
         }
      
        stage("Build Frontend") {
            steps {
                sh "cd frontend && npm ci && npm install && npm run build"
		            sh "cd .."
            }
        }

        stage("Build Backend") {
            steps {
                sh "cd backend && npm ci && npm install"
		            sh "cd .."
            }
        }

        stage("Build Docker Images") {
            steps {
                sh "docker build -t projectlightfeather-frontend:$BUILD_NUMBER -f frontend/Dockerfile ./frontend"
                sh "docker build -t projectlightfeather-backend:$BUILD_NUMBER -f backend/Dockerfile ./backend"
            }
        }
	
	    
       stage ('Publish to ECR') {
            steps {
              sh '''
              aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/c6p1p1z3
        
              docker tag projectlightfeather-frontend:$BUILD_NUMBER public.ecr.aws/c6p1p1z3/proj-frontend:$BUILD_NUMBER
              
              docker push public.ecr.aws/c6p1p1z3/proj-frontend:$BUILD_NUMBER
              
              docker tag projectlightfeather-backend:$BUILD_NUMBER public.ecr.aws/c6p1p1z3/proj-backend:$BUILD_NUMBER
              docker push public.ecr.aws/c6p1p1z3/proj-backend:$BUILD_NUMBER
              '''
            }
          } 
    }
  
  post {
        always {
	          cleanWs()
        }
   }
}
