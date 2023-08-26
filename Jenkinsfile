pipeline {
    agent any
    
    environment {
        PATH = "${HOME}/miniconda/bin:${PATH}"
    }
    
    stages {
        stage('Setup Miniconda') {
            steps {
                script {
                    sh """
                    rm -rf /var/lib/jenkins/miniconda  # Delete existing installation
                    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh
                    bash miniconda.sh -b -p ${HOME}/miniconda
                    conda init bash
                    """
                }
            }
        }
        
        stage('Create and Activate Conda Environment') {
            steps {
                script {
                    sh """
                    conda create -y -n chatbot_env python=3.8
                    """
                }
            }
        }
        
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Activate Conda Environment') {
            steps {
                script {
                    sh "conda run -n chatbot_env pip install -r requirements.txt"
                    sh "conda run -n chatbot_env pip install tensorflow"
                    sh "conda run -n chatbot_env python -m nltk.downloader punkt"  // Download NLTK resources
                }
            }
        }
        
        stage('Train Chatbot Model') {
            steps {
                script {
                    sh "conda run -n chatbot_env python train_chatbot_model.py"
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t chatbot-app ."
                }
            }
        }
        
        stage('Run Docker Container') {
            steps {
                script {
                    sh "docker run -p 8080:80 chatbot-app &"
                }
            }
        }
    }
    
    post {
        always {
            sh "docker ps -aqf \"name=chatbot-app\" | xargs docker stop"
            sh "docker system prune -f"
            sh "conda deactivate"
        }
    }
}
