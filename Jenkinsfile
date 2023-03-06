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
                    // from the git plugin
                    // https://www.jenkins.io/doc/pipeline/steps/git/
                    git 'https://github.com/samuelomonedo247/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition.git'
                    try {
                        container('gradle') {
                            sh '''
                                cd Chapter08/sample1
                                chmod +x gradlew
                                ./gradlew clean build
                            '''
                        }
                    } catch (err) {
                        currentBuild.result = 'FAILURE'
                        throw err
                    }
                    if (env.BRANCH_NAME != 'master' && env.BRANCH_NAME != 'feature') {
                        echo "Skipping tests for branch ${env.BRANCH_NAME}"
                    } else {
                        container('gradle') {
                            sh '''
                                cd Chapter08/sample1
                                chmod +x gradlew
                                if [ "$BRANCH_NAME" == "feature" ]; then
                                    ./gradlew clean test --tests="*Test" -x jacocoTestReport
                                else
                                    ./gradlew clean test --tests="*Test"
                                fi
                            '''
                        }
                    }
                }
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
