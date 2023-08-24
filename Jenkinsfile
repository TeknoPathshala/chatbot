pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup Environment') {
            steps {
                script {
                    // Install Miniconda
                    sh 'wget -O miniconda.sh https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh'
                    sh 'chmod +x miniconda.sh'
                    sh './miniconda.sh -b -p $WORKSPACE/miniconda'
   
                    // Update conda
                    sh '$WORKSPACE/miniconda/bin/conda update -y -n base conda'
   
                    // Create Conda environment
                    sh '$WORKSPACE/miniconda/bin/conda create -y -n chatbot python=3.8'
                }
            }
        }
        
        stage('Update Pip and Setuptools') {
            steps {
                script {
                    sh '$WORKSPACE/miniconda/bin/conda run -n chatbot pip install --upgrade pip setuptools'
                }
            }
        }
        
        stage('Install Dependencies') {
            steps {
                script {
                    sh '$WORKSPACE/miniconda/bin/conda run -n chatbot pip install -r requirements.txt'
                }
            }
        }
        
        stage('Train Chatbot Model') {
            steps {
                script {
                    sh '$WORKSPACE/miniconda/bin/conda run -n chatbot python train_chatbot_model.py'
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image
                    sh "docker build -t chatbot-app ."
                }
            }
        }
        
        stage('Run Docker Container') {
            steps {
                script {
                    // Run Docker container
                    sh "docker run -p 8080:80 chatbot-app &"
                }
            }
        }
    }
    
    post {
        always {
            // Stop the Docker container
            sh "docker ps -aqf \"name=chatbot-app\" | xargs docker stop"
            
            // Clean up Docker resources
            sh "docker system prune -f"
            
            // Deactivate Conda environment
            sh '$WORKSPACE/miniconda/bin/conda deactivate'
        }
    }
}
