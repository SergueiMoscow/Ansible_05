pipeline {
    agent any

    environment {
        VENV_DIR   = 'venv'
        REPO_DIR   = './'
    }

    stages {
        stage('Setup Virtual Environment') {
            steps {
                script {
                    // Создание виртуального окружения, если оно еще не существует
                    if (!fileExists(VENV_DIR)) {
                        echo "Creating virtual environment..."
                        sh "python3 -m venv ${VENV_DIR}"
                    } else {
                        echo "Virtual environment already exists. Skipping creation."
                    }
                }
            }
        }

        stage('Install dependencies') {
            steps {
                dir(REPO_DIR) {
                    script {
                        echo "Activating virtual environment and installing requirements..."
                        sh """
                        source ${VENV_DIR}/bin/activate
                        pip install -U setuptools pip
                        pip install molecule
                        """
                    }
                }
            }
        }

        stage('Molecule setup') {
            steps {
                dir(REPO_DIR) {
                    script {
                        if (fileExists("molecule/default")) {
                            echo "Scenario directory molecule/default exists. Removing it..."
                            sh 'rm -rf molecule/default'
                        }
                        echo "Initializing new Molecule scenario..."
                        sh """
                        source ${VENV_DIR}/bin/activate
                        molecule init scenario default
                        """
                    }
                }
            }
        }

        stage('Molecule test') {
            steps {
                dir(REPO_DIR) {
                    script {
                        echo "Executing Molecule tests..."
                        sh """
                        source ${VENV_DIR}/bin/activate
                        molecule test
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
            echo "Pipeline execution finished."
        }
        success {
            echo "Pipeline successfully executed!"
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
