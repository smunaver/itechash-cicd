pipeline {
    agent any

    stages {
        stage('Build & Publish') {
            agent {
                docker {
                    image 'mcr.microsoft.com/dotnet/sdk:8.0'
                    reuseNode true 
                }
            }
            steps {
                script {
                    def services = [
                        'ApiGateway', 'Attendance.API', 'Asset.API', 
                        'Employee.API', 'Identity.API', 'Leave.API', 
                        'MasterData.API', 'Payroll.API'
                    ]

                    def changedFiles = ""
                    try {
                        changedFiles = sh(script: "git diff --name-only HEAD~1 HEAD", returnStdout: true).trim()
                    } catch (e) {
                        changedFiles = "FORCE_ALL"
                    }

                    // If no files changed (empty string), also force all for the first time
                    if (!changedFiles) { changedFiles = "FORCE_ALL" }

                    services.each { name ->
                        if (changedFiles.contains(name) || changedFiles.contains("docker-compose.yml") || changedFiles == "FORCE_ALL") {
                            echo "--- Publishing ${name} ---"
                            sh "dotnet publish ${name}/${name}.csproj -c Release -o ./${name}/"
                        }
                    }
                }
            }
        }

        stage('Docker Deploy') {
            steps {
                echo 'Refreshing containers on localhost...'
                // Using hyphenated version for older Docker versions
                sh 'docker-compose up -d --build'
            }
        }
    }
}
