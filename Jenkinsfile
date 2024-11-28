pipeline {
    agent any
   environment {
       USER = "ubuntu"
       HOSTNAME = "3.80.208.247"
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
                        rsync -avzP -e "ssh -i ${SSH_KEY_FILE} -o StrictHostKeyChecking=no" target/maven-jar-sample-1.0.jar $USER@$HOSTNAME:/home/ubuntu/java-app/
                        ssh -o StrictHostKeyChecking=no -i ${SSH_KEY_FILE} $USER@$HOSTNAME sudo /usr/bin/systemctl restart java-app.service
                    '''
                }
            }
        }

        stage('upload artifact to nexus'){
            def version = "1.0.0"
            steps{
                    nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: 'http://3.80.208.247:8081/repository/test-maven/',
        groupId: 'test-maven-group',
        version: version,
        repository: 'test-maven',
        credentialsId: 'nexus',
        artifacts: [
            [artifactId: projectName,
             classifier: '',
             file: 'my-service-' + env.version + '.jar',
             type: 'jar']
        ]
        )
            }
        }
                
            
        }
        
}
