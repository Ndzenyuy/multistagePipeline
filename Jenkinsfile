pipeline{
    agent any
    tools {
        maven "maven3"
        jdk "OpenJDK-11"
    }
    stages{
        stage('UNIT TEST'){
            steps {
                sh 'mvn test'
            }
        }

        stage ("Build"){
            steps{
                sh 'echo Build stage'
            }
        }

        stage ("Deploy to stage"){
            steps{
                sh 'echo deploy to stage'
            }
        }

        stage ("Deploy to prod"){
            steps{
                sh 'echo Deploy to  prod'
            }
        }
    }

}