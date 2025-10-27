pipeline {
    agent any

    environment {
        // Name for the venv folder
        VENV_DIR = "venv"
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout code from the repo (Jenkins will do this automatically for multibranch/SCM jobs)
                checkout scm
                echo "Checked out branch: ${env.BRANCH_NAME}"
            }
        }

        stage('Setup Python venv') {
            steps {
                // Create virtualenv and upgrade pip
                bat """
                if exist %VENV_DIR% rmdir /S /Q %VENV_DIR%
                python -m venv %VENV_DIR%
                call %VENV_DIR%\\Scripts\\activate
                python -m pip install --upgrade pip
                """
            }
        }

        stage('Install dependencies') {
            steps {
                // If you have requirements.txt, install it
                bat """
                if exist requirements.txt (
                    call %VENV_DIR%\\Scripts\\activate
                    pip install -r requirements.txt
                ) else (
                    echo "No requirements.txt found, skipping pip install"
                )
                """
            }
        }

        stage('Run Python script') {
            steps {
                // Run ex1.py
                bat """
                call %VENV_DIR%\\Scripts\\activate
                python ex1.py
                """
            }
        }

        stage('Archive') {
            steps {
                // Archive output files (change pattern as needed)
                archiveArtifacts artifacts: '**/*.html, **/*.log, **/*.txt', allowEmptyArchive: true
            }
        }
    }

    post {
        always {
            // Cleanup the venv folder (optional)
            bat 'if exist %VENV_DIR% rmdir /S /Q %VENV_DIR%'
        }
        success {
            echo 'Pipeline succeeded'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
