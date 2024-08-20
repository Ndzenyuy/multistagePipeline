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
                    
        }

        stage('BUILD DOCKER IMAGE'){            
            steps {
                sh 'docker buildx build --tag ndzenyuy/ecommerce_app:latest --file Docker-files/app/Dockerfile .'
            }
            
        }

        stage('PUBLISH DOCKER IMAGE'){            
            steps {
                script {
                    withDockerRegistry([ credentialsId: "dockerlogin", url: ""]){
                        sh 'docker push ndzenyuy/ecommerce_app:latest'
                        sh 'docker rmi ndzenyuy/ecommerce_app:latest'
                    }
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