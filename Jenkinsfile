pipeline {
    agent any

    environment {
        JAVA_HOME = "/usr/lib/jvm/java-17-openjdk-amd64"
        PATH = "${JAVA_HOME}/bin:${env.PATH}"
    }
	
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/csjeevan11/java-project.git'
            }
        }

        stage('Build') {
            steps {
                dir('sample-app') {
                    sh 'mvn clean package'
                }
            }
        }
		stage('Deploy to Tomcat') {
			steps {
			withCredentials([sshUserPrivateKey(credentialsId: 'tomcat-ssh',
											  keyFileVariable: 'SSH_KEY',
											  usernameVariable: 'SSH_USER')]) {
					sh '''
						echo "Remove old WAR in Tomcat Server..."

                        ssh -i $SSH_KEY -o StrictHostKeyChecking=no \
						$SSH_USER@172.31.44.10 \
						"sudo systemctl stop tomcat"
                        
                        ssh -i $SSH_KEY -o StrictHostKeyChecking=no \
						$SSH_USER@172.31.44.10 \
                        "sudo rm -rf /opt/tomcat/webapps/sample.war /opt/tomcat/webapps/sample"

						echo "Deploy WAR to Tomcat..."

						scp -i $SSH_KEY -o StrictHostKeyChecking=no sample-app/target/sample.war \
						$SSH_USER@172.31.44.10:/opt/tomcat/webapps/sample.war

						echo "Restarting Tomcat..."

						ssh -i $SSH_KEY -o StrictHostKeyChecking=no \
						$SSH_USER@172.31.44.10 \
						"sudo systemctl restart tomcat"
					'''
					
				}
			}

		}
            
    }
}
