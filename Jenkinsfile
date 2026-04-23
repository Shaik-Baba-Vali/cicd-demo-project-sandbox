pipeline {

    agent any

    tools {
        maven 'Maven'    // must match name in Jenkins Global Tool Configuration
        jdk   'JDK'      // must match name in Jenkins Global Tool Configuration
    }

    environment {
        CLIENT_ID     = credentials('ANYPOINT_CLIENT_ID')
        CLIENT_SECRET = credentials('ANYPOINT_CLIENT_SECRET')
        BG_ID         = 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'  // paste your Business Group ID here
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
                    mvn clean package -DskipTests ^
                        -s C:\\Windows\\system32\\config\\systemprofile\\.m2\\settings.xml
                """
            }
        }

        stage('Test') {
            steps {
                bat """
                    mvn test ^
                        -s C:\\Windows\\system32\\config\\systemprofile\\.m2\\settings.xml
                """
            }
        }

        stage('Deploy to CloudHub 2.0') {
            steps {
                bat """
                    mvn deploy -DmuleDeploy ^
                        -s C:\\Windows\\system32\\config\\systemprofile\\.m2\\settings.xml ^
                        -DconnectedApp.clientId=%CLIENT_ID% ^
                        -DconnectedApp.clientSecret=%CLIENT_SECRET% ^
                        -Dcloudhub2.businessGroupId=%BG_ID%
                """
            }
        }

    }

    post {
        success { echo 'Deployed successfully to CloudHub 2.0!' }
        failure { echo 'Pipeline failed. Check logs above.' }
        always  { cleanWs() }
    }

}
