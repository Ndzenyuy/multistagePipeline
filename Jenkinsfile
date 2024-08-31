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

        stage ('Sonar Cloud') {
        environment {
            scannerHome = tool 'sonarserver'
        }
            steps{
                withSonarQubeEnv('sonarscanner') {
                    sh "${scannerHome}/bin/sonar-scanner \
                    -Dsonar.organization=emart-webapp \
                    -Dsonar.projectKey=Ndzenyuy_multistagePipeline \
                    -Dsonar.sources=src/ \
                    -Dsonar.junit.reportsPath=target/surefire-reports/ \
                    -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                    -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml \
                    -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/"
                }
            }
        }

        stage('BUILD DOCKER IMAGE'){            
            steps {
                sh 'docker buildx build --tag ndzenyuy/ecommerce_app-:latest --file Docker-files/app/Dockerfile .'
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