pipeline {
    agent any
   environment {
       USER = "ubuntu"
       HOSTNAME = "172.31.84.149"
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

        stage('code scan'){
            steps{
                withSonarQubeEnv(installationName: 'sonarqube') { 
                    sh 'mvn clean sonar:sonar'
            }
        }

        }


        stage ('build'){
            steps{
                sh '''
                mvn clean package
            
                '''
            }
        }
        //stage('test'){
            //steps{
            //sh  'mvn test'
            //}
        //}
        
        stage('deploy'){

            steps{

                script {
                    def pom = readFile('pom.xml')
                    def parsedPom = new XmlSlurper().parseText(pom)
                    env.POM_VERSION = parsedPom.version.text()
                }

                withCredentials([sshUserPrivateKey(credentialsId: 'SSHKEY', keyFileVariable: 'SSH_KEY_FILE')]) {
                    sh '''
                        rsync -avzP -e "ssh -i ${SSH_KEY_FILE} -o StrictHostKeyChecking=no" target/maven-jar-sample-"${POM_VERSION}".jar $USER@$HOSTNAME:/home/ubuntu/java-app/
                        ssh -o StrictHostKeyChecking=no -i ${SSH_KEY_FILE} $USER@$HOSTNAME sudo /usr/bin/systemctl restart java-app.service
                    '''
                }
            }
        }

        stage('upload artifact to nexus'){
            steps{
                    nexusArtifactUploader(
        nexusVersion: 'nexus3',
        protocol: 'http',
        nexusUrl: '172.31.84.149:8081/repository/test-maven/',
        groupId: 'test-maven-group',
        version: "${env.POM_VERSION}",
        repository: 'test-maven',
        credentialsId: 'nexus',
        artifacts: [
            [artifactId: "${JOB_NAME}",
             classifier: '',
             file: 'target/maven-jar-sample-' + env.POM_VERSION + '.jar',
             type: 'jar']
        ]
        )
            }
        }
                
            
        }
        
}
