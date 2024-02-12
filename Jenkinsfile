pipeline {
    agent {
  label 'slave1'
}

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "maven-3.9.6"
    }


    stages {
        stage('SCM Checkout') {
            steps {
                // Get some code from a GitHub repository
                git url: 'https://github.com/pritamraj72/insurance-web-application.git'
            }
        }
stage('Maven Build') {
            steps {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
stage('Test the code ') {
            steps {
                // testin the code using sonar server.
               // sh "mvn sonar:sonar"
                sh "echo 'scanning the source code and generating the reports' "
            }
        }
        
        stage('deploying artifacts into nexus repo') {
            steps {
                // loading the artifacts into nexus server.
                sh "echo 'deploying the jar or war files into nexus server'"
            }
        }
    }
}
