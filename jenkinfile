pipeline{
    agent any
    environment {
        SONARQUBE_ENV = tool 'SonarScanner'
    }
    stages{
        stage("Code clone from github"){
            steps{
               git url: "https://github.com/var-priya/Wanderlust_with_DevSecOps.git", branch:"main"
             }
        }
        stage("SonarQube Analysis"){
             steps{
               withSonarQubeEnv("Sonar"){
                    sh "$SONARQUBE_ENV/bin/sonar-scanner -Dsonar.projectName=wanderlust -Dsonar.projectKey=wanderlust"
                }
             }
        }
        stage("Owasp dependency check"){
             steps{
               dependencyCheck additionalArguments:'--scan ./' , odcInstallation: 'Owasp'
               dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
             }
        }
        stage("Sonar Quality Gate Scan"){
             steps{
               timeout(time: 2, unit: 'MINUTES'){
                   waitForQualityGate abortPipeline: false
               }
             }
        }
        stage("Trivy File system Scan"){
             steps{
               sh "trivy fs --format table -o trivy-fs-report.html ."
             }
        }
        stage("Deploy using docker compose "){
             steps{
               sh "docker-compose up -d"
             }
        }
        
 }
}
