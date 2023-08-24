pipeline {
    agent any
    
    environment {
        VIRTUAL_ENV = "${WORKSPACE}/venv"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup Environment') {
            steps {
                script {
                    // Install Python and create a virtual environment
                    sh 'python3 -m venv $VIRTUAL_ENV'
                    sh "$VIRTUAL_ENV/bin/pip install -U pip"
                }
            }
        }
        
        stage('Install Dependencies') {
            steps {
                script {
                    // Activate virtual environment and install dependencies
                    sh "$VIRTUAL_ENV/bin/pip install -r requirements.txt"
                }
            }
        }
        
        stage('Train Chatbot Model') {
            steps {
                script {
                    // Activate virtual environment and train the model
                    sh "source $VIRTUAL_ENV/bin/activate && python train_chatbot_model.py"
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh "docker build -t chatbot-app ."
                }
            }
        }
        
        stage('Run Docker Container') {
            steps {
                script {
                    // Run the Docker container
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
            
            // Deactivate virtual environment
            sh "$VIRTUAL_ENV/bin/deactivate"
        }
    }
}
