pipeline {
    agent {
        node {
            label 'maven'

       }
    }

    stages {
        stage("Build") {
            steps {
                sh 'mvn clean deploy'

            }
       }
        stage('SonarQube Analysis') {
        enviornment {
          scannerHome = tool 'sonar-scanner'
        }
        steps{
        withSonarQubeEnv('sonarqube-server') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
        }
   }
    }

}
