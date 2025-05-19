pipeline {
    agent any
    
    tools {
        // Вказуємо назву MSBuild конфігурації в Jenkins
        msbuild 'MSBuild'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup NuGet') {
            steps {
                bat '''
                    powershell -Command "Invoke-WebRequest -Uri 'https://dist.nuget.org/win-x86-commandline/latest/nuget.exe' -OutFile 'nuget.exe'"
                '''
            }
        }
        
        stage('Restore Packages') {
            steps {
                bat '''
                    if not exist packages mkdir packages
                    nuget.exe restore test_repos.sln -PackagesDirectory packages
                '''
            }
        }
        
        stage('Build') {
            steps {
                // Jenkins автоматично додасть MSBuild в PATH
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
            junit testResults: 'test_report.xml', allowEmptyResults: true
        }
    }
}