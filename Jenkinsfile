pipeline {
      tools {

       maven "MAVEN_LOCAL"

    }
    agent any
    stages {
        stage ('Build Backend'){
            steps{
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage ('Unit Tests'){
            steps{
                sh 'mvn test'
            }
        }
        stage ('Sonar Analysis'){
            environment {
                scannerHome = tool 'SONAR_SCANNER'
            }
            steps{
                withSonarQubeEnv('SONAR_LOCAL'){
                    sh "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://127.0.0.1:9000 -Dsonar.login=76302f81ca49ee7dff236e14537e1dd48fcaaa05 -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/mvn**,**/src/test**,**/model/**,**/Application.java"
                }


                
            }
        }

        stage ('Quality Gate'){
            steps{
                timeout(time: 1, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
