pipeline {

    agent any

    tools {
        maven 'Maven'
        jdk 'JDK'
    }

    environment {
        CLIENT_ID     = credentials('ANYPOINT_CLIENT_ID')
        CLIENT_SECRET = credentials('ANYPOINT_CLIENT_SECRET')

        BG_ID = '3265c39d-1b19-4158-828c-58479d052eea'
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
                    -DskipMunitTests ^
                    -s settings.xml
                """
            }
        }

        stage('Deploy to CloudHub 2.0') {
            steps {
                bat """
                    mvn deploy -DmuleDeploy ^
                    -DconnectedApp.clientId=%CLIENT_ID% ^
                    -DconnectedApp.clientSecret=%CLIENT_SECRET% ^
                    -Dcloudhub2.environment=Sandbox ^
                    -Dcloudhub2.target=CloudHub 2.0 ^
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
