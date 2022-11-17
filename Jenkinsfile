pipeline {
	agent none
	stages {
		stage('Integration UI Test') {
			parallel {
				stage('Deploy') {
					agent any
					steps {
						sh 'chmod +x ./jenkins/scripts/deploy.sh'
						sh './jenkins/scripts/deploy.sh'
						input message: 'Finished using the web site? (Click "Proceed" to continue)'
						sh 'chmod +x ./jenkins/scripts/kill.sh'
						sh './jenkins/scripts/kill.sh'
					}
				}
				stage('Headless Browser Test') {
					agent {
						docker {
							image 'maven:3-alpine'
							args '-v /root/.m2:/root/.m2'
						}
					}
					stage('OWASP DependencyCheck') {
						steps {
							dependencyCheck additionalArguments: '--format HTML --format XML', odcInstallation: 'Default'
						}
					}
					post {
						always {
							junit 'target/surefire-reports/*.xml'
						}
					}
				}
			}
		}
	}
}
