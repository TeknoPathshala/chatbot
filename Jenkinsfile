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
                    wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O miniconda.sh
                    rm -rf /var/lib/jenkins/miniconda  # Remove existing directory if exists
                    bash miniconda.sh -b -p /var/lib/jenkins/miniconda
                    export CONDA_PREFIX=/var/lib/jenkins/miniconda
                    """
                }
            }
        }
        
        stage('Create and Activate Conda Environment') {
            steps {
                script {
                    sh """
                    conda create -y -n chatbot_env python=3.8
                    conda activate chatbot_env
                    """
                }
            }
        }
        
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Install Dependencies') {
            steps {
                script {
                    sh "pip install -r requirements.txt"
                    sh "pip install tensorflow"
                }
            }
        }
        
        stage('Train Chatbot Model') {
            steps {
                script {
                    sh "python train_chatbot_model.py"
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
            sh 'conda deactivate'
        }
    }
}
