pipeline{
    agent any
    tools {

        maven "maven3"              // configure both openjdk and maven in manage jenkins -> tools let both names match these
        jdk "openjdk-17"

    }

    // to configure build trigger for multibranch pipeline, install "Multibranch Scan Webhook Trigger" plugin
    // add the token, then copy the url to github webhooks and use it as the webhook link to trigger pipeline

    environment{
        /*registryCreds = 'dockerlogin'
        registry = "https://hub.docker.com" */

        AWS_REGION = 'eu-west-3' // Specify your AWS region
        ECR_REPOSITORY = '781655249241.dkr.ecr.eu-west-3.amazonaws.com/emartapp'       
        ECR_REGISTRY = "https://9781655249241.dkr.ecr.eu-west-3.amazonaws.com"
        service = "vproappstagesvc"
        cluster = "vproappcluster"
    }

   

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
                       dockerImage = docker.build( ${ECR_REPOSITORY} + "/ecommerce:${BUILD_ID}",  ".")               
                    }
            }
        }

        /*
        stage('Upload App Image') {  //upload to dockerhub
          steps{
            script {
               withDockerRegistry([ credentialsId: registryCredential, url: ecrRegistry]){
                dockerImage.push("$BUILD_NUMBER")
                dockerImage.push('latest')
               }              
              
            }
          }
        } */

        stage('Build and Upload app Image'){  //upload to ecr, Install the plugin "AWS steps", and store aws credentials
            steps{
                script {
                   script {
                    // Use the AWS Credentials
                    withAWS(credentials: 'aws-ecr-creds', region: "${AWS_REGION}") {
                        // Authenticate to ECR
                        def ecrLogin = sh(script: "aws ecr get-login-password --region ${AWS_REGION}", returnStdout: true).trim()
                        sh "echo ${ecrLogin} | docker login --username AWS --password-stdin ${ECR_REPOSITORY}"

                        // Build the Docker image
                        // def dockerImage = docker.build("${ECR_REPOSITORY}:${BUILD_ID}")

                        // Push the Docker image to ECR
                        dockerImage.push("${BUILD_NUMBER}")
                        dockerImage.push('latest')
                    }
                }
                }
            }
        }


        stage ("CLEAN WORKSPACE"){
            steps{
                sh 'docker rmi ${ECR_REPOSITORY}:${BUILD_ID}'                

                sh 'rm -rf target/'                
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