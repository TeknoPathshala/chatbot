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
        
        stage('Install Dependencies and Run Tasks') {
            steps {
                script {
                    sh """
                    conda create -y -n chatbot_env python=3.8
                    source activate chatbot_env
                    pip install -r requirements.txt
                    pip install tensorflow
                    python train_chatbot_model.py
                    source deactivate
                    """
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
