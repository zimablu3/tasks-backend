pipeline {
      tools {

       maven "M2_HOME"

    }
    agent any
    stages {
        stage ('Build Backend'){
            steps{
                sh 'mvn clean package -DskipTest=true'
            }
        }
    }
}