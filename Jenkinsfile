pipeline {
    agent any
    stages {
        stage("Compile") {
            steps {
                sh "chmod +x gradlew"
                sh "./gradlew compileJava"
            }
        }
        stage("Unit test") {
            steps {
                sh "./gradlew test"
            }
        }
        stage("Static code analysis"){
            steps {
                sh "./gradlew checkstyleMain"
                publishHTML (target: [
                    reportDir: 'build/reports/checkstyle/',
                    reportFiles: 'main.html',
                    reportName: 'Checkstyle Report'
                ])
            }
        }
        stage("Code coverage"){
            steps {
                sh "./gradlew jacocoTestReport"
                publishHTML (target: [
                    reportDir: 'build/reports/jacoco/test/html',
                    reportFiles: 'index.html',
                    reportName: "JaCoCo Report"
                ])
                sh "./gradlew jacocoTestCoverageVerification"
            }
        }
        stage("Package") {
            steps {
                sh "./gradlew build"
            }
        }
        stage("Docker build") {
            steps{
                sh "sudo docker build -t suryazi/calculator ."
            }
        }
        stage("Deploy to staging") {
            steps {
                sh "sudo chmod +x /usr/local/bin/docker-compose"
                sh "/usr/local/bin/docker-compose up -d"
            }
        }
        stage("Acceptance test") {
            steps {
                sh "/usr/local/bin/docker-compose -f docker-compose.yml -f acceptance/docker-compose-acceptance.yml build test"
                sh "/usr/local/bin/docker-compose -f docker-compose.yml -f acceptance/docker-compose-acceptance.yml -p acceptance up -d"
                sh "test ${docker wait acceptance_test_1} -eq 0"
            }
        }
    }
    post {
        always {
            sh "/usr/local/bin/docker-compose -f docker-compose.yml -f acceptance/docker-compose-acceptance.yml -p acceptance down"
        }
    }
}