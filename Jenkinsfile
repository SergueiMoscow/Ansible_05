pipeline {
    agent any

    environment {
        REPO_URL   = 'git@github.com:SergueiMoscow/Ansible_05.git'
        REPO_DIR   = 'Ansible_05'
        VENV_DIR   = 'venv'
    }

    stages {
        stage('Preparation') {
            steps {
                script {
                    if (fileExists(REPO_DIR)) {
                        echo "Directory ${REPO_DIR} exists. Performing git pull..."
                        dir(REPO_DIR) {
                            sh 'git pull'
                        }
                    } else {
                        echo "Directory ${REPO_DIR} does not exist. Cloning the repository..."
                        sh "git clone ${REPO_URL} ${REPO_DIR}"
                        dir(REPO_DIR) {
                            sh "python3 -m venv ${VENV_DIR}"
                        }
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
