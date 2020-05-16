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
                sh "sudo docker-compose up -d"
            }
        }
        stage("Acceptance test") {
            steps {
                sleep 60
                sh "sudo chmod u+x acceptance_test.sh"
                sh "sudo ./acceptance_test.sh"
            }
        }
    }
    post {
        always {
            sh "sudo docker-compose down"
        }
    }
}