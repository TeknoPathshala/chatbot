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
        
        stage('Deploy Chatbot App') {
            steps {
                script {
                    // Activate virtual environment and start the app
                    sh "source $VIRTUAL_ENV/bin/activate && python app.py &"
                }
            }
        }
    }
    
    post {
        always {
            // Stop the chatbot app
            sh 'pkill -f "python app.py"'
            
            // Deactivate virtual environment
            sh "$VIRTUAL_ENV/bin/deactivate"
        }
    }
}
