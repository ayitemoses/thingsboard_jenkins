pipeline {
    agent any

    tools {
        jdk 'JDK17'
        maven 'Maven3'
    }

    options {
        timestamps()
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Application') {
            steps {
                bat 'mvn clean install -T6 -DskipTests -Dlicense.skip=true'
            }
        }

        stage('Run Application Tests') {
            steps {
                bat """
                mvn test -pl application ^
                  -Dtest="!**/nosql/*Test.java,!org.thingsboard.server.controller.**,!org.thingsboard.server.edge.**,!org.thingsboard.server.service.**,!org.thingsboard.server.transport.mqtt.**,!org.thingsboard.server.transport.coap.**,!org.thingsboard.server.transport.lwm2m.**" ^
                  -DforkCount=6 ^
                  -Dparallel=packages ^
                  -Dsurefire.rerunFailingTestsCount=2 ^
                  -Dsurefire.failOnFlakeCount=5
                """
            }
        }
    }

    post {
        always {
            junit '**/target/surefire-reports/*.xml'
        }
    }
}
