pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Setup NuGet') {
            steps {
                bat '''
                    powershell -Command "if (-not (Test-Path 'nuget.exe')) { Invoke-WebRequest -Uri 'https://dist.nuget.org/win-x86-commandline/latest/nuget.exe' -OutFile 'nuget.exe' }"
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
                bat '''
                    "C:\\Program Files\\Microsoft Visual Studio\\2022\\Community\\MSBuild\\Current\\Bin\\MSBuild.exe" test_repos.sln /p:Configuration=Debug /p:Platform=x64
                '''
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