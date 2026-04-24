pipeline {

    agent any

    tools {
        maven 'Maven'
        jdk 'JDK'
    }

    environment {
        ANYPOINT_CLIENT_ID     = credentials('ANYPOINT_CLIENT_ID')
        ANYPOINT_CLIENT_SECRET = credentials('ANYPOINT_CLIENT_SECRET')
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
                    mvn clean deploy -DmuleDeploy ^
                    -DconnectedApp.clientId=%ANYPOINT_CLIENT_ID% ^
                    -DconnectedApp.clientSecret=%ANYPOINT_CLIENT_SECRET% ^
                    -s settings.xml
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
