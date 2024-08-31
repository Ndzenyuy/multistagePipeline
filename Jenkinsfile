pipeline{
    agent any
    tools {
        maven "maven3"              // configure both openjdk and maven in manage jenkins -> tools let both names match these
        jdk "OpenJDK-17"
    }

    // to configure build trigger for multibranch pipeline, install "Multibranch Scan Webhook Trigger" plugin
    // add the token, then copy the url to github webhooks and store it there

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


        stage ('CODE ANALYSIS WITH SONARQUBE') {
        environment {
            scannerHome = tool 'sonarserver'
        }
            steps{
                withSonarQubeEnv('sonarserver') {
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

                sh 'docker buildx build --tag ndzenyuy/ecommerce_app-${BUILD_ID}:latest --file Docker-files/app/Dockerfile .'
            }
            
        } 


        stage('PUBLISH DOCKER IMAGE'){            
            steps {
                script {
                    withDockerRegistry([ credentialsId: "dockerlogin", url: ""]){
                        sh 'docker push ndzenyuy/ecommerce_app-${BUILD_ID}:latest'
                        sh 'docker rmi ndzenyuy/ecommerce_app-${BUILD_ID}:latest'
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