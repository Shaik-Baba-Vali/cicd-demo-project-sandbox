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

        stage('Build & Publish to Exchange') {
            steps {
                bat """
                    mvn clean deploy ^
                    -DskipTests ^
                    -s settings.xml
                """
            }
        }

        stage('Deploy to CloudHub 2.0') {
            steps {
                bat """
                    mvn mule:deploy -DmuleDeploy ^
                    -DconnectedApp.clientId=%CLIENT_ID% ^
                    -DconnectedApp.clientSecret=%CLIENT_SECRET% ^
                    -s settings.xml
                """
            }
        }

    }

    post {
        success {
            echo "✅ Deployment SUCCESS"
        }
        failure {
            echo "❌ Deployment FAILED"
        }
        always {
            cleanWs()
        }
    }
}
