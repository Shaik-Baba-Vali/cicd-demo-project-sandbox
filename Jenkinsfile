pipeline {

    agent any

    tools {
        maven 'Maven'
        jdk 'JDK'
    }

    environment {
        CLIENT_ID     = credentials('ANYPOINT_CLIENT_ID')
        CLIENT_SECRET = credentials('ANYPOINT_CLIENT_SECRET')
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
                    mvn clean package ^
                    -DskipTests ^
                    -s settings.xml
                """
            }
        }

        stage('Deploy to CloudHub 2.0') {
            steps {
                bat """
                    mvn mule:deploy -DmuleDeploy -s settings.xml^
                    -DconnectedApp.clientId=%CLIENT_ID% ^
                    -DconnectedApp.clientSecret=%CLIENT_SECRET% 
                """
            }
        }

    }

    post {
        success {
            echo "✅ Deployment SUCCESS to CloudHub 2.0"
        }
        failure {
            echo "❌ Deployment FAILED"
        }
        always {
            cleanWs()
        }
    }
}
