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
                    // Activate existing Conda environment
                    sh """
                    . /var/lib/jenkins/workspace/chatbot/miniconda/bin/activate
                    """
                }
            }
        }
        
        stage('Create Virtual Environment') {
            steps {
                script {
                    sh """
                    . /var/lib/jenkins/workspace/chatbot/miniconda/bin/activate
                    conda create -y -n chatbot_env python=3.8
                    conda activate chatbot_env
                    """
                }
            }
        }
        
        stage('Update Pip and Setuptools') {
            steps {
                script {
                    sh """
                    . /var/lib/jenkins/workspace/chatbot/miniconda/bin/activate
                    conda run -n chatbot_env pip install --upgrade pip setuptools
                    """
                }
            }
        }
        
        stage('Install Dependencies') {
            steps {
                script {
                    sh """
                    . /var/lib/jenkins/workspace/chatbot/miniconda/bin/activate
                    conda run -n chatbot_env pip install -r requirements.txt
                    pip install tensorflow
                    """
                }
            }
        }
        
        stage('Train Chatbot Model') {
            steps {
                script {
                    sh """
                    . /var/lib/jenkins/workspace/chatbot/miniconda/bin/activate
                    conda activate chatbot_env
                    python train_chatbot_model.py
                    """
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
            
            // Deactivate Conda environment and virtual environment
            sh '. /var/lib/jenkins/workspace/chatbot/miniconda/bin/deactivate'
            sh 'conda deactivate'
        }
    }
}
