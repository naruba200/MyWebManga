pipeline {
    agent any

    stages {
        stage('Clone') {
            steps {
                echo '🔄 Cloning source code from GitHub...'
                git branch: 'main', url: 'https://github.com/naruba200/MyWebManga.git'
            }
        }

        stage('Copy to IIS Folder') {
            steps {
                echo '📁 Copying static files to IIS directory...'
                bat '''
                    if exist "C:\\wwwroot\\mymangaweb" (
                        xcopy "%WORKSPACE%\\*" "C:\\wwwroot\\mymangaweb" /E /Y /I /R
                    ) else (
                        mkdir "C:\\wwwroot\\mymangaweb"
                        xcopy "%WORKSPACE%\\*" "C:\\wwwroot\\mymangaweb" /E /Y /I /R
                    )
                '''
            }
        }

        stage('Deploy to IIS') {
            steps {
                echo '🌐 Setting up IIS website...'
                powershell '''
                    Import-Module WebAdministration
                    $siteName = "MyHtmlSite"
                    $path = "C:\\wwwroot\\mymangaweb"
                    $port = 81

                    if (-not (Test-Path IIS:\\Sites\\$siteName)) {
                        New-Website -Name $siteName -Port $port -PhysicalPath $path -Force
                        Write-Host "✅ Website $siteName created on port $port."
                    } else {
                        Write-Host "ℹ️ Website $siteName already exists."
                    }
                '''
            }
        }
    }

    post {
        success {
            echo '🎉 Deployment successful!'
        }
        failure {
            echo '❌ Deployment failed!'
        }
    }
}
