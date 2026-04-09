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

                    // Force build if it's a new workspace or no files detected
                    if (!changedFiles) { changedFiles = "FORCE_ALL" }

                    services.each { name ->
                        if (changedFiles.contains(name) || changedFiles.contains("docker-compose.yml") || changedFiles == "FORCE_ALL") {
                            echo "--- Publishing ${name} ---"
                            // Publish to the root of the service folder so your Dockerfile finds the DLLs
                            sh "dotnet publish ${name}/${name}.csproj -c Release -o ./${name}/"
                        }
                    }
                }
            }
        }

        stage('Docker Deploy') {
            steps {
                echo 'Refreshing containers on localhost...'
                // DOCKER_BUILDKIT=0 and COMPOSE_DOCKER_CLI_BUILD=0 bypass the Buildx requirement
                sh 'export DOCKER_BUILDKIT=0 && export COMPOSE_DOCKER_CLI_BUILD=0 && docker-compose up -d --build'
            }
        }
    }
}
