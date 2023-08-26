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
        
        stage('Install Dependencies') {
            steps {
                script {
                    sh """
                    conda activate chatbot_env
                    pip install -r requirements.txt
                    pip install tensorflow
                    """
                }
            }
        }
        
        stage('Train Chatbot Model') {
            steps {
                script {
                    sh """
                    conda activate chatbot_env
                    python train_chatbot_model.py
                    """
                }
            }
        }
        
        // Add other stages as needed
        
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
                    sh "docker run -p 8080:80 --name chatbot-container chatbot-app &"
                }
            }
        }
    }
    
    post {
        always {
            script {
                def containerId = sh(
                    script: "docker ps -aqf 'name=chatbot-container'",
                    returnStdout: true
                ).trim()

                if (containerId) {
                    sh "docker stop ${containerId}"
                }

                sh "docker system prune -f"
            }
        }
    }
}
