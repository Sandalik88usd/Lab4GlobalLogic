pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup') {
            steps {
                script {
                    // Завантажуємо NuGet напряму
                    bat '''
                        if not exist "nuget.exe" (
                            powershell -Command "Invoke-WebRequest -Uri 'https://dist.nuget.org/win-x86-commandline/latest/nuget.exe' -OutFile 'nuget.exe'"
                        )
                    '''
                }
            }
        }
        
        stage('Restore NuGet Packages') {
            steps {
                bat '''
                    if not exist "packages" mkdir packages
                    nuget.exe restore test_repos.sln -PackagesDirectory packages
                '''
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
            junit testResultsPattern: 'test_report.xml', allowEmptyResults: true
        }
    }
}