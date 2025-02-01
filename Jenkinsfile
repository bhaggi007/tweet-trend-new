pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    environment {
        PATH = "/opt/apache-maven-3.9.9/bin:$PATH"
    }

    stages {
        stage("build"){
            steps {
                echo "-----Build started-----"
                sh "mvn clean deploy -Dmaven.test.skip=true"
                echo "-----Build completed-----"
            }
        }
        stage("test"){
            steps{
                echo "-----Unit test started-----"
                sh 'mvn surefire-report:report'
                echo "-----Unit test completed-----"
            }
        }
        stage('SonarQube analysis') {
            environment{
                scannerHome = tool 'bhaggi-sonar-scanner'
            }
            steps{
                withSonarQubeEnv('bhaggi-sonarqube-server') { // If you have configured more than one global server connection, you can specify its name
        sh "${scannerHome}/bin/sonar-scanner"
        }
            }
        
        
  }
        stage('Quality Gate'){
            steps{
                script{
                    timeout(time: 1, unit:'HOURS'){
                        def qg = waitForQualityGate()
                        if(qg.status != 'OK') {
                            error "Pipeline aborted due to quality gate failure: ${qg.status}"
                        }
                    }
                }
            }
        }
    }
}
