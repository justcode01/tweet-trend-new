pipeline {
    agent {
        node {
            label 'maven'

       }
    }

    stages {
        stage("Build") {
            steps {
                sh 'mvn clean deploy -Dmaven.test.skip=true'

            }
       }
       stage("unit-test"){
             steps{
                sh 'mvn surefire-report:report'
             }
       }
        stage('SonarQube Analysis') {
        environment {
          scannerHome = tool 'sonar-scanner'
        }
        steps{
        withSonarQubeEnv('sonarqube-server') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
        }
        }
    def registry = 'https://justcode01.jfrog.io'
       stage("Jar Publish") {
        steps {
            script {
                    echo '<--------------- Jar Publish Started --------------->'
                     def server = Artifactory.newServer url:registry+"/artifactory" ,  credentialsId:"jfrog-cred"
                     def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}";
                     def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "libs-release-local/{1}",
                              "flat": "false",
                              "props" : "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""
                     def buildInfo = server.upload(uploadSpec)
                     buildInfo.env.collect()
                     server.publishBuildInfo(buildInfo)
                     echo '<--------------- Jar Publish Ended --------------->'  
            
            }
        }   
    }
    }

}
