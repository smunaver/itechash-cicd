pipeline {
    agent any

    stages {
        stage('Build & Publish') {
            // This runs the .NET SDK container to compile your code
            agent {
                docker { 
                    image 'mcr.microsoft.com/dotnet/sdk:8.0' 
                    reuseNode true
                }
            }
            steps {
                script {
                    def services = [
                        'ApiGateway': '5000', 'Attendance.API': '5001', 
                        'Asset.API': '5002', 'Employee.API': '5003', 
                        'Identity.API': '5004', 'Leave.API': '5005', 
                        'MasterData.API': '5006', 'Payroll.API': '5007'
                    ]

                    // Get list of changed files
                    def changedFiles = sh(script: "git diff --name-only HEAD~1 HEAD", returnStdout: true).trim().split('\n')

                    services.each { name, port ->
                        // Check if files in the service folder or the root compose/Jenkinsfile changed
                        if (changedFiles.any { it.startsWith(name + "/") } || changedFiles.contains("docker-compose.yml")) {
                            echo "Building and Publishing ${name}..."
                            
                            // Publish directly into the service folder so the Dockerfile 'COPY . .' works
                            sh "dotnet publish ${name}/${name}.csproj -c Release -o ./${name}/out"
                            
                            // Move files up so they are in the root of the build context as expected by your Dockerfile
                            sh "mv ./${name}/out/* ./${name}/ && rm -rf ./${name}/out"
                        }
                    }
                }
            }
        }

        stage('Docker Deploy') {
            steps {
                echo 'Updating localhost containers...'
                // This command runs on the host via the mapped docker.sock
                sh 'docker-compose up -d --build'
            }
        }
    }

    post {
        success {
            echo "Successfully updated microservices on localhost."
        }
    }
}
