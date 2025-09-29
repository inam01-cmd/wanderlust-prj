pipeline {
    agent any
    environment {
        SONAR_HOME = tool "sonar-qube"   // ✅ Correct spelling, must match Tool name
    }
    stages {
        stage("Code Clone Git Hub") {
            steps {
                git url: "https://github.com/inam01-cmd/wanderlust-prj.git", branch: "main"
            }
        }
        stage("SonarQube Quality Analysis") {
            steps {
                withSonarQubeEnv("sonar") {   // ✅ Must match the Server Name in Manage Jenkins → System
                    sh """
                        $SONAR_HOME/bin/sonar-scanner \
                          -Dsonar.projectKey=wanderlust \
                          -Dsonar.projectName=wanderlust \
                          -Dsonar.sources=.
                    """
                }
            }
        }
        stage("OWASP Dependency Check") {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'Dependency-Check'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage("sonar Quality Gate Scan"){
            steps{
                timeout(time: 2, unit: "MINUTES"){
                    waitForQualityGate abortPipeline: false
                }
            }
        }
        stage("Trivy File System Scan") {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        stage("Deploy app with Docker Compose"){
            steps {
                sh " docker compose up -d "
            }
        }
    }
}
