pipeline {
    agent any
    stages {
        stage('Verify Python Path') {
            steps {
                sh """
                    echo "=== 验证Python版本 ==="
                    python3 --version
                    echo "Python路径验证通过！"
                """
            }
        }
        stage('Checkout') {
            steps {
                git url: './', branch: 'main'
            }
        }
        stage('Fix pip') {
            steps {
                sh """
                    echo "=== 修复pip环境 ==="
                    python3 -m ensurepip --upgrade
                    python3 -m pip --version
                """
            }
        }
        stage('Install Dependencies') {
            steps {
                sh """
                    python3 -m pip install --upgrade pip
                    python3 -m pip install -r requirements.txt
                """
            }
        }
        stage('Lint') {
            steps {
                sh "python3 -m flake8 app.py tests/"
            }
        }
        stage('Test') {
            steps {
                sh """
                    python3 -m pytest --cov=app tests/ --cov-report=html
                """
            }
            post {
                always {
                    publishHTML(target: [
                        allowMissing: false,
                        alwaysLinkToLastBuild: false,
                        keepAll: true,
                        reportDir: 'htmlcov',
                        reportFiles: 'index.html',
                        reportName: 'Coverage Report'
                    ])
                }
            }
        }
        stage('Build') {
            steps {
                sh """
                    python3 -m pip install pyinstaller
                    pyinstaller --onefile app.py
                """
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying application...'
                sh "python3 app.py &"
            }
        }
    }
    post {
        success { echo 'CI/CD pipeline completed successfully!' }
        failure { echo 'CI/CD pipeline failed!' }
    }
}
