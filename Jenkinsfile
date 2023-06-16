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
    }
}