pipeline{
	agent any
	stages{
		stage('Clone'){
			steps{
				sh '''
					git clone https://github.com/maheshmt22/webhookassign.git
				'''
			}
		}
		stage('Maven Build'){
		steps{
			sh ''' 
				cd /home/ubuntu/javacodrohit/devopscode/
				mvn clean package
				sudo rm -rf /opt/tomcat/webapps/tomcat-demo
				sudo rm -rf /opt/tomcat/webapps/tomcat-demo.war
				cd target
				cp *.war /opt/tomcat/webapps/
			'''
			}
		}
		stage('Restart the tomcat service'){
		steps{
			sh ''' 
				sudo systemctl restart tomcat
			'''
			}
		}
	}
}
