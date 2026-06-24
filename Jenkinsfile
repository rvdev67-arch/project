pipeline {
    agent { label 'built-in' }

    stages {

        stage('Checkout') {
            steps {
                git 'https://github.com/rvdev67-arch/project.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'target/*.war'
            }
        }

        stage('Stash Artifact') {
            steps {
                stash includes: 'target/*.war', name: 'war-file'
            }
        }

        stage('Deploy to slave1') {
            agent { label 'slave1' }

            steps {
                unstash 'war-file'

                sh '''
                echo "Deploying WAR to Tomcat..."

                sudo cp target/*.war /opt/tomcat/webapps/ROOT.war

                echo "Restarting Tomcat..."

                sudo /opt/tomcat/bin/shutdown.sh
                sleep 5
                sudo /opt/tomcat/bin/startup.sh

                echo "Deployment completed"
                '''
            }
        }
    }
}
