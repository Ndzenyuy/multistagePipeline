pipeline{
    agent any
    tools {
        maven "maven3"              // configure both openjdk and maven in manage jenkins -> tools let both names match these
        jdk "openjdk-17"
    }

    // to configure build trigger for multibranch pipeline, install "Multibranch Scan Webhook Trigger" plugin
    // add the token, then copy the url to github webhooks and use it as the webhook link to trigger pipeline

    stages{
        stage('UNIT TEST'){                         // make sure maven3 is configured in tools
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

        stage ('CODE ANALYSIS WITH SONARQUBE') {                    // create a project on sonarcloud.io and link it
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
            // requires the following plugins: ## docker pipeline, ## cloudbees docker build and publish 
            // no configuration required after plugins installation
            steps{
                script{
                    dockerImage = docker.build("ndzenyuy/ecommerce:${BUILD_ID}",  ".")               
                    }
            }
        }


        stage('PUBLISH DOCKER IMAGE'){                      // store registery creds in dockerlogin  (jenkins)   
            steps {
                script {
                        dockerImage.push(${BUILD_ID})                                         
                }
            }                       

        }

        /*stage ("CLEAN WORKSPACE"){
            steps{
                sh 'docker rmi ndzenyuy/ecommerce_app:${BUILD_ID}'
                sh 'ls'
                sh 'rm -rf target/'
                sh 'ls'
            }
        } */

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