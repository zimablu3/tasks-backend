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
                sleep(30)                
                timeout(time: 1, unit:'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage ('Deploy Backend'){
            steps{
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }

        stage ('API Test'){
            steps{
                dir('api-test') {
                    git 'https://github.com/zimablu3/tasks-api-test'
                    sh 'mvn test'
                }
            }
        }

        stage ('Deploy Frontend') {
            steps{
                dir('frontend') {
                    git 'https://github.com/zimablu3/tasks-frontend'
                    sh 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
            }
        }

         stage ('Functional Test'){
            steps{
                dir('functional-test') {
                    git 'https://github.com/zimablu3/tasks-functional-tests'
                    sh 'mvn test'
                }
            }
        }

        stage('Deploy Prod') {
            steps {
                sh 'docker-compose build'
                sh 'docker-compose up -d'
            }
        }

        stage ('Health Check'){
            steps{
                sleep(15)
                dir('functional-test') {                    
                    sh 'mvn verify -Dskip.surefire.tests'
                }
            }
        }
    }
    post {
        always {
            junit allowEmptyResults: true, testResults: 'target/surefire-reports/*.xml, api-test/target/surefire-reports/*.xml,functional-test/target/surefire-reports/*.xml, functional-test/target/failsafe-reports/*.xml'
            archiveArtifacts artifacts: 'target/tasks-backend.war, frontend/target/tasks.war', followSymlinks: false, onlyIfSuccessful: true
        }
        unsuccessful {
            emailext attachLog: true, body: 'See the attached log below', subject: 'Build $BUILD_NUMBER has failed', to: 'warenreaper@gmail.com'
        }
        fixed {
            emailext attachLog: true, body: 'See the attached log below', subject: 'Build is fine!!!', to: 'warenreaper@gmail.com'
        }
    }
}

