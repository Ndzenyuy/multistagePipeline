pipeline{
    agent any
    stages{
        stage ("Test") {
            steps{
                sh 'echo test stage'
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