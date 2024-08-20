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
        stage('INTEGRATION TEST'){
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }
		
        stage ('CODE ANALYSIS WITH CHECKSTYLE'){
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
            post {
                success {
                    echo 'Generated Analysis Result'
                }
            }
        }

        stage ('BUILD ARTIFACT'){
            
            steps {
                sh 'mvn -DskipTests install'
            }
            post {
                success {
                    echo "Now Archiving."
                    archiveArtifacts artifacts: '**/*.war'
                }
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