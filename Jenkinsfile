pipeline {
    agent any

    stages {
        stage('Build & Publish') {
            steps {
                script {
                    def services = [
                        'ApiGateway': '5000', 'Attendance.API': '5001', 
                        'Asset.API': '5002', 'Employee.API': '5003', 
                        'Identity.API': '5004', 'Leave.API': '5005', 
                        'MasterData.API': '5006', 'Payroll.API': '5007'
                    ]

                    // Get list of changed files between the last two commits
                    // If this is the first build, it might need 'git diff --name-only HEAD^ HEAD'
                    def changedFiles = sh(script: "git diff --name-only HEAD~1 HEAD || git ls-files", returnStdout: true).trim().split('\n')

                    services.each { name, port ->
                        if (changedFiles.any { it.startsWith(name + "/") } || changedFiles.contains("docker-compose.yml")) {
                            echo "--- Processing ${name} ---"
                            
                            // 1. Restore & Publish using the host's dotnet (or inside the container)
                            sh "dotnet publish ${name}/${name}.csproj -c Release -o ./${name}/publish"
                            
                            // 2. Clean up and prepare files for Docker 'COPY . .'
                            // We move published files to the root of the service folder
                            sh "cp -r ./${name}/publish/* ./${name}/"
                        }
                    }
                }
            }
        }

        stage('Docker Deploy') {
            steps {
                echo 'Updating localhost containers via Docker Compose...'
                // This uses the docker.sock you mapped to the host
                sh 'docker-compose up -d --build'
            }
        }
    }

    post {
        always {
            // Optional: Clean up publish folders to keep the workspace tidy
            sh 'find . -name "publish" -type d -exec rm -rf {} + || true'
        }
    }
}
