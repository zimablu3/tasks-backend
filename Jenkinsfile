pipeline {
    agent any
    stages {
        stage ('Build Backend'){
            steps{
                sh 'mvn package -DskipTest=true'
            }
        }
    }
}