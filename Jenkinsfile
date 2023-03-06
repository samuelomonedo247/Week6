pipeline {
    agent none
    environment {
        POD_LABEL = 'build'
    }
    stages {
        stage('Checkout') {
            agent any
            steps {
                checkout scm
            }
        }
        stage('Build and Test') {
            agent any
            when {
                expression {
                    return env.BRANCH_NAME != 'playground'
                }
            }
            steps {
                node(POD_LABEL) {
                    container('gradle') {
                        git 'https://github.com/samuelomonedo247/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition.git'
                        sh '''
                            cd Chapter08/sample1
                            chmod +x gradlew
                            ./gradlew clean build
                        '''
                            if (env.BRANCH_NAME != 'master' && env.BRANCH_NAME != 'feature') {
                                echo "Skipping tests for branch ${env.BRANCH_NAME}"
                            } else {
                                if (env.BRANCH_NAME == 'feature') {
                                    sh '''
                                        cd Chapter08/sample1
                                        chmod +x gradlew
                                        ./gradlew clean test --tests="*Test" -x jacocoTestReport
                                    '''
                                } else {
                                    sh '''
                                        cd Chapter08/sample1
                                        chmod +x gradlew
                                        ./gradlew clean test --tests="*Test"
                                    '''
                                }
                            }
        stage('Build Container') {
            agent any
            when {
                expression {
                    return env.BRANCH_NAME != 'playground' && currentBuild.result == 'SUCCESS'
                }
            }
            steps {
                node(POD_LABEL) {
                    container('kaniko') {
                        sh '''
                            echo 'FROM openjdk:8-jre' > Dockerfile
                            echo 'COPY ./Chapter08/sample1/calculator-0.0.1-SNAPSHOT.jar app.jar' >> Dockerfile
                            echo 'ENTRYPOINT ["java", "-jar", "app.jar"]' >> Dockerfile
                            mv /mnt/Chapter08/sample1/calculator-0.0.1-SNAPSHOT.jar .
                            /kaniko/executor --context `pwd` --destination somonedo/hello-kaniko:1.0
                        '''
                    }
                }
            }
        }
    }
}