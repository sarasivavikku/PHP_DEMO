pipeline {
   agent none
  environment{
       IMAGE_NAME='vikranth2/java-mvn-privaterepos:php$BUILD_NUMBER'
       deploy_server='ec2-user@172.31.22.3'
        Build4='ec2-user@172.31.21.87'
   }
    stages {          
        stage('BUILD DOCKERIMAGE AND PUSH TO DOCKERHUB') {
            agent any            
            steps {
                script{
                sshagent(['build4']) {
                withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                echo "Packaging the apps"
                sh "scp -o StrictHostKeyChecking=no docker-compose-script.sh ec2-user@172.31.21.87:/home/ec2-user"
                sh "ssh -o StrictHostKeyChecking=no ${Build4} 'bash ~/docker-files/docker-script.sh'"
                sh "ssh ${Build4} sudo docker build -t ${IMAGE_NAME} /home/ec2-user/docker-files/"
                sh "ssh ${Build4} sudo docker login -u $USERNAME -p $PASSWORD"
                sh "ssh ${Build4} sudo docker push ${IMAGE_NAME}"
              }
            }
            }
        }
        }
       stage('DEPLOY DOCKER CONATINER USING DOCKER_COMPOSE'){
           agent any
           steps{
               script{
                    sshagent(['Deploy_server']){
                         withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                         sh "scp -o StrictHostKeyChecking=no -r docker-files ${deploy_server}:/home/ec2-user"
                         sh "ssh -o StrictHostKeyChecking=no ${deploy_server} 'bash ~/docker-files/docker-script.sh'"
                         sh "ssh ${deploy_server} sudo docker login -u $USERNAME -p $PASSWORD"
                         sh "ssh ${deploy_server} bash /home/ec2-user/docker-files/docker-compose-script.sh ${IMAGE_NAME}"
                         }
                    }
               }
           }
       }
    }
}
