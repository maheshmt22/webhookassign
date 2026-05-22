pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }

    environment {
        SERVER_IP   = '54.159.31.22'
        SERVER_USER = 'ubuntu'
        TOMCAT_DIR  = '/opt/tomcat/webapps'
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
                            script: "basename target/*.war",
                            returnStdout: true
                        ).trim()

                        echo "WAR Name: ${WAR_FILE}"
                    }
                }
            }
        }

        stage('Deploy to Tomcat') {
            steps {

                sshagent(['ubuntu']) {

                    sh """
                        set -e

                        echo "Copying WAR to server..."

                        scp -o StrictHostKeyChecking=no \
                        devopscode/target/${WAR_FILE} \
                        ${SERVER_USER}@${SERVER_IP}:/tmp/

                        echo "Deploying WAR on Tomcat server..."

                        ssh -o StrictHostKeyChecking=no \
                        ${SERVER_USER}@${SERVER_IP} << EOF

                            sudo systemctl stop tomcat

                            sudo rm -rf ${TOMCAT_DIR}/tomcat-demo*

                            sudo mv /tmp/${WAR_FILE} ${TOMCAT_DIR}/

                            sudo systemctl start tomcat

                            sudo systemctl status tomcat --no-pager

                        EOF
                    """
                }
            }
        }
    }
}
