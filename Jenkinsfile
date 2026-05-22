pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    stages {

        stage('Checkout Code') {
             steps {
        git branch: 'main',
        url: 'https://github.com/maheshmt22/webhookassign'
    }
        }

        stage('Build') {
            steps {
                dir('devopscode') {
                    sh 'mvn clean package -DskipTests'
                }
            }
        }
    }
}
