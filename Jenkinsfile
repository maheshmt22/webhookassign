pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    environment {
        SERVER_IP = '54.159.31.22'
        SERVER_USER = 'ubuntu'
        TOMCAT_DIR = '/opt/tomcat/webapps'
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/maheshmt22/webhookassign.git'
            }
        }

        stage('Build Application') {
            steps {

                dir('devopscode') {

                    sh 'mvn clean package -DskipTests'

                    script {

                        env.WAR_FILE = sh(
                            script: "ls target/*.war",
                            returnStdout: true
                        ).trim()

                        env.WAR_NAME = sh(
                            script: "basename ${env.WAR_FILE}",
                            returnStdout: true
                        ).trim()

                        echo "WAR File Path: ${env.WAR_FILE}"
                        echo "WAR Name: ${env.WAR_NAME}"
                    }
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {

                sshagent(credentials: ['tomcat-ssh-key']) {

                    sh """
                        set -e

                        echo "Deploying WAR: ${WAR_NAME}"

                        echo "Copying WAR to server..."

                        scp -o StrictHostKeyChecking=no \
                        ${WAR_FILE} \
                        ${SERVER_USER}@${SERVER_IP}:/tmp/

                        echo "Deploying to Tomcat..."

                        ssh -o StrictHostKeyChecking=no \
                        ${SERVER_USER}@${SERVER_IP} '

                            set -e

                            echo "Removing old deployment..."

                            sudo rm -rf ${TOMCAT_DIR}/devopscode*

                            echo "Moving new WAR..."

                            sudo mv /tmp/${WAR_NAME} ${TOMCAT_DIR}/

                            echo "Restarting Tomcat..."

                            sudo systemctl restart tomcat

                            sleep 10

                            sudo systemctl status tomcat --no-pager
                        '

                        echo "Deployment completed successfully!"
                    """
                }
            }
        }
    }
}
