pipeline {
    agent any

    stages {
        stage('Clone repository') {
            steps {
                git 'https://github.com/samuelomonedo247/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition.git'
            }
        }

        stage('Build and test') {
            steps {
                script {
                    sh './gradlew jacocoTestCoverageVerification'
                    sh './gradlew CheckstyleMain'
                    sh './gradlew test'
                }
            }
        }
    }
}
