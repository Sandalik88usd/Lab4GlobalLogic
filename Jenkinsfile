pipeline {
    agent any
    
    tools {
        msbuild 'MSBuild'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Install C++ Build Tools') {
            steps {
                script {
                    // Перевіряємо, чи встановлені C++ компоненти
                    def vcExists = bat(
                        script: '''
                            if exist "C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\MSBuild\\Microsoft\\VC\\v170\\Microsoft.Cpp.Default.props" (
                                echo EXISTS
                            ) else (
                                echo MISSING
                            )
                        ''',
                        returnStdout: true
                    ).trim()
                    
                    if (vcExists.contains('MISSING')) {
                        echo 'Installing Visual C++ Build Tools...'
                        bat '''
                            "C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\Common7\\Tools\\VsDevCmd.bat" && ^
                            "C:\\Program Files (x86)\\Microsoft Visual Studio\\Installer\\vs_installer.exe" modify ^
                            --installPath "C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools" ^
                            --add Microsoft.VisualStudio.Workload.VCTools ^
                            --add Microsoft.VisualStudio.Component.VC.Tools.x86.x64 ^
                            --add Microsoft.VisualStudio.Component.Windows11SDK.22000 ^
                            --quiet --wait --norestart
                        '''
                    } else {
                        echo 'Visual C++ Build Tools already installed.'
                    }
                }
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
                bat '''
                    call "C:\\Program Files (x86)\\Microsoft Visual Studio\\2022\\BuildTools\\Common7\\Tools\\VsDevCmd.bat"
                    msbuild test_repos.sln /p:Configuration=Debug /p:Platform=x64
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