pipipeline {
    agent any
    stages {
      stage('Run pipeline against a gradle project - test MAIN') {
	container('gradle') {
          stage('Build a gradle project')
              // from the git plugin
              // https://www.jenkins.io/doc/pipeline/steps/git/
              git 'https://github.com/samuelomonedo247/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition.git'
              sh '''
              cd Chapter08/sample1
              chmod +x gradlew
              ./gradlew test
              ./gradlew build
			  
		  stage("Code coverage") {
              try {
			      sh '''
				  pwd
                  cd Chapter08/sample1
                  ./gradlew jacocoTestCoverageVerification
                  ./gradlew jacocoTestReport
                  '''
			  } catch (Exception E) {
                  echo 'Failure detected'
			  }	  
				  
		  {
            echo "I am the ${env.BRANCH_NAME} branch"
          }
          stage('Code coverage') {
	    sh 'printenv'
            echo "My CC branch is: ${env.CHANGE_BRANCH}"
            if (env.BRANCH_NAME == "feature") {
              echo "I am the ${env.BRANCH_NAME} branch"
            }
          }
        }
      }   
    }
}
