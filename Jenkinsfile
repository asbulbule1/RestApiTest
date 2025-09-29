pipeline {
    agent any

    tools {
        maven 'maven'   // Use the Maven name you configured in Jenkins
        jdk 'JDK21'        // Use the JDK name you configured in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Preserve History') {
            steps {
                script {
                    if (fileExists("target/allure-report/history")) {
                        echo "Copying history from previous build..."
                        bat """
                            if not exist target\\allure-results\\history mkdir target\\allure-results\\history
                            xcopy /E /I /Y target\\allure-report\\history target\\allure-results\\history
                        """
                    } else {
                        echo "No history folder found in previous build."
                    }
                }
            }
        }

        stage('Run Tests') {
            steps {
                bat "mvn clean test"
            }
        }

        stage('Allure Report') {
            steps {
                allure([
                    includeProperties: false,
                    jdk: '',
                    results: [[path: "target/allure-results"]]
                ])
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'target/allure-results/**', fingerprint: true
        }
    }
}
