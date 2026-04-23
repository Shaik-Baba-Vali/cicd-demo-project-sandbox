pipeline {
    agent any

    tools {
        maven 'Maven'    // must match name in Jenkins Global Tool Configuration
        jdk   'JDK11'    // Mule 4.x requires JDK 11
    }

    environment {
        // Make sure these match EXACTLY with your Jenkins credential names
        CLIENT_ID     = credentials('CONNECTED_APP_CLIENT_ID')
        CLIENT_SECRET = credentials('CONNECTED_APP_CLIENT_SECRET')
        BG_ID         = '3265c39d-1b19-4158-828c-58479d052eea'
        // Optional: Set your desired CloudHub 2.0 region and provider
        CLOUDHUB_REGION = 'us-east-1'
        CLOUDHUB_PROVIDER = 'MC'  // MC = MuleSoft Control Plane, AWS, AZURE, etc.
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat """
                    mvn clean compile -U ^
                        -DskipTests
                """
            }
        }

        stage('Test') {
            steps {
                bat "mvn test"
            }
        }

        stage('Deploy to CloudHub 2.0') {
            steps {
                withEnv(["BUILD_NUMBER=${env.BUILD_NUMBER}"]) {
                    bat """
                        mvn deploy ^
                            -DmuleDeploy ^
                            -DconnectedApp.clientId=%CLIENT_ID% ^
                            -DconnectedApp.clientSecret=%CLIENT_SECRET% ^
                            -Dcloudhub2.businessGroupId=%BG_ID% ^
                            -Dcloudhub2.provider=%CLOUDHUB_PROVIDER%
                    """
                }
            }
        }
    }

    post {
        success { 
            echo 'Deployed successfully to CloudHub 2.0!' 
            // Optional: Send email notification
        }
        failure { 
            echo 'Pipeline failed. Check logs above.'
            // Optional: Send alert
        }
        always { 
            cleanWs() 
        }
    }
}
