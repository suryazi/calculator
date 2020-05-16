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
                sh "sudo docker build -t calculator:1 ."
            }
        }
        stage("Push image to OCIR") {
            steps {
                sh "sudo docker login -u 'suryazi' -p '_UQ24zDUY{IG-I-pf[{<' iad.ocir.io"
                sh "sudo docker tag calculator:1 iad.ocir.io/ocid1.tenancy.oc1..aaaaaaaa3rnceftry4u6fpki4bgz7qlt363vi5olq5ji4ip3jxbxmugge6pa/calculator:custom"
                sh "sudo docker push iad.ocir.io/ocid1.tenancy.oc1..aaaaaaaa3rnceftry4u6fpki4bgz7qlt363vi5olq5ji4ip3jxbxmugge6pa/calculator:custom"
            }
        }
    }
}