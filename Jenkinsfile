pipeline {
    agent any

    stages {

        stage('Clone') {
            steps {
                echo '🔄 Cloning source code from GitHub...'
                git branch: 'main', url: 'https://github.com/naruba200/MyWebManga.git'
            }
        }

        stage('Restore Packages') {
            steps {
                echo '📦 Restoring NuGet packages...'
                bat 'dotnet restore'
            }
        }

        stage('Build') {
            steps {
                echo '🛠️ Building project...'
                bat 'dotnet build --configuration Release'
            }
        }

        stage('Run Tests') {
            steps {
                echo '✅ Running tests...'
                bat 'dotnet test --no-build --verbosity normal'
            }
        }

        stage('Publish to Folder') {
            steps {
                echo '📤 Publishing to ./publish folder...'
                bat 'dotnet publish -c Release -o ./publish'
            }
        }

        stage('Copy to IIS Folder') {
            steps {
                echo '📁 Copying to IIS directory (C:\\wwwroot\\mymangaproject)...'
                // Nếu cần stop IIS để ghi đè thì thêm `iisreset /stop`
                bat '''
                    if exist "C:\\wwwroot\\mymangaproject" (
                        xcopy "%WORKSPACE%\\publish" "C:\\wwwroot\\mymangaproject" /E /Y /I /R
                    ) else (
                        mkdir "C:\\wwwroot\\mymangaproject"
                        xcopy "%WORKSPACE%\\publish" "C:\\wwwroot\\mymangaproject" /E /Y /I /R
                    )
                '''
            }
        }

        stage('Deploy to IIS') {
            steps {
                echo '🌐 Deploying to IIS (port 81)...'
                powershell '''
                    Import-Module WebAdministration

                    $siteName = "MySite"
                    $physicalPath = "C:\\wwwroot\\mymangaproject"
                    $port = 81

                    if (-not (Test-Path "IIS:\\Sites\\$siteName")) {
                        New-Website -Name $siteName -Port $port -PhysicalPath $physicalPath -Force
                        Write-Host " Website $siteName created."
                    } else {
                        Write-Host "ℹ Website $siteName already exists."
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
