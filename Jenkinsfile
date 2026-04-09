pipeline {
    agent any

    stages {
        stage('Build & Publish') {
            // This tells Jenkins to run this stage inside a .NET SDK container
            agent {
                docker {
                    image 'mcr.microsoft.com/dotnet/sdk:8.0'
                    // Reuse the workspace so files are available for the next stage
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

                    // Determine changes or force build if it's the first run
                    def changedFiles = ""
                    try {
                        changedFiles = sh(script: "git diff --name-only HEAD~1 HEAD", returnStdout: true).trim()
                    } catch (e) {
                        echo "Initial build or no history found. Building all services."
                        changedFiles = "ALL"
                    }

                    services.each { name ->
                        if (changedFiles.contains(name) || changedFiles.contains("docker-compose.yml") || changedFiles == "ALL") {
                            echo "--- Publishing ${name} ---"
                            // Publish into the service folder so your Dockerfile 'COPY . .' works
                            sh "dotnet publish ${name}/${name}.csproj -c Release -o ./${name}/"
                        }
                    }
                }
            }
        }

        stage('Docker Deploy') {
            steps {
                echo 'Refreshing containers on localhost...'
                // Using 'docker compose' (no hyphen) is standard for modern installs
                // --no-build is optional here since you published the DLLs already
                sh 'docker compose up -d --build'
            }
        }
    }

    post {
        success {
            echo "Deployment to localhost successful!"
        }
    }
}
