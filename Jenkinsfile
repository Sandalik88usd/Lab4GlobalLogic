pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Build') {
            steps {
                bat 'msbuild test_repos.sln /p:Configuration=Debug /p:Platform=x64'
            }
        }
        
        stage('Test') {
            steps {
                bat 'x64\\Debug\\test_repos.exe --gtest_output="xml:test_report.xml"'
            }
        }
    }
    
    post {
        always {
            // Правильний синтаксис для junit
            junit testResults: 'test_report.xml', allowEmptyResults: true
        }
    }
}