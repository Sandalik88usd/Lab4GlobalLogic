pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Restore NuGet Packages') {
            steps {
                script {
                    // Відновлюємо NuGet пакети
                    bat 'nuget restore test_repos.sln'
                }
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
            publishTestResults testResultsPattern: 'test_report.xml'
        }
    }
}