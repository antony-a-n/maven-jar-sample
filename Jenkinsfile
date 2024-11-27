pipeline {
    agent any
   environment {
       USER = "ubuntu"
       HOSTNAME = "54.166.0.27"
   }
       
    stages {
        stage('cleanws'){
            steps{
                cleanWs()
            }
            
        }
        
        stage('checkout scm'){
            steps{
                git branch: 'pipeline', url: 'https://github.com/antony-a-n/maven-jar-sample.git'
            }
        }
        stage ('build'){
            steps{
                sh '''
                mvn clean package
            
                '''
                echo "building"
            }
        }
        stage('test'){
            steps{
            sh  'mvn test'
            }
        }
        
        stage('deploy'){
            steps{
                withCredentials([sshUserPrivateKey(credentialsId: 'SSHKEY', keyFileVariable: 'SSH_KEY_FILE')]) {
                    sh '''
                        echo "Using SSH Key File: ${SSH_KEY_FILE}"
                        rsync -avzP -e "ssh -i ${SSH_KEY_FILE} -o StrictHostKeyChecking=no" target/maven-jar-sample-1.0-SNAPSHOT.jar $USER@$HOSTNAME:/home/ubuntu/java-app/
                        ssh -o StrictHostKeyChecking=no -i ${SSH_KEY_FILE} $USER@$HOSTNAME sudo /usr/bin/systemctl restart java-app.service
                    '''
                }
            }
        }
                
            
        }
        
}
