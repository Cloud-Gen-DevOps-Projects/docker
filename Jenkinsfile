pipeline {
    
    agent any
    
    stages {
        
        stage("git login"){
            steps{
                git 'https://github.com/Devops9AM/Docker.git'
            }
        }
        // stage("Sending docker file to ansible server"){
          //  steps{
           //     sshagent(['ansible']) {
           //      sh 'ssh -o StrictHostKeyChecking=no root@192.168.254.128'
           //      sh 'scp /var/lib/jenkins/workspace/docker_image/* root@192.168.254.128:/opt/ansible'
          //      }
          //  }
       // }
         stage("docker build"){
             steps{
        sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
        sh 'docker image tag $JOB_NAME:v1.$BUILD_ID thanish/$JOB_NAME:v1.$BUILD_ID'
        sh 'docker image tag $JOB_NAME:v1.$BUILD_ID thanish/$JOB_NAME:latest'
             }
         }
         stage("push Image: DOCKERHUB"){
             steps{
                 withCredentials([string(credentialsId: 'docker_pwd', variable: 'docker_passwd')]) {
                 sh "docker login -u thanish -p ${docker_passwd}"
                sh 'docker image push thanish/$JOB_NAME:v1.$BUILD_ID'
                sh 'docker image push thanish/$JOB_NAME:latest'
               //A number of images will get stored into our jenkins server so need to remove prev build images
                //local images,taged images & latest images all delete 
              sh 'docker image rm $JOB_NAME:v1.$BUILD_ID thanish/$JOB_NAME:v1.$BUILD_ID thanish/$JOB_NAME:latest'
              }
             }
         }
           stage("Docker Container Deployment"){
                steps {
        // def docker_run = 'docker run -p 9000:80 -d --name scripted-pipeline-demo thanish/scripted-pipeline-demo:latest'
        //def docker_rmv_container = 'docker run -p 9000:80 -d --name scripted-pipeline-demo thanish/scripted-pipeline-demo:latest'
        //def docker_rmi = 'docker rmi -f thanish/scripted-pipeline-demo'
        // container deployment need to be done on remote host server DOCKER-Host so ssh-Agent plugin required in jenkins
       sshagent(['docker_passwd']) {
    sh "ssh -o StrictHostKeyChecking=no root@192.168.254.128 docker rm -f scripted-pipeline-demo"
    sh "ssh -o StrictHostKeyChecking=no root@192.168.254.128 docker rmi -f thanish/scripted-pipeline-demo"
    sh "ssh -o StrictHostKeyChecking=no root@192.168.254.128 docker run -p 9000:80 -d --name scripted-pipeline-demo thanish/scripted-pipeline-demo:latest"
    //Once sucess try with Docker-Host_IP:Port
    //Once after Changing the Dockerfile it says port already allocated to prev container whom we didn't yet deleted
    // therefor ist delete the prev container so that port will be free then try creating a new 
    //But still it'll not update because latest image is already in server it's not going to dockerHub to pull latest commmit so need to remove images also
       }
                }
             }
    
          
        /* stage("Building Docker Image"){
            steps{
                script {
                     sshagent(['ansible']) {
                sh 'echo $PWD'
                sh 'ssh -t -t root@192.168.254.128 -o StrictHostKeyChecking=no "echo pwd && cd /opt/ansible && echo pwd"'
               //sh 'ssh -o StrictHostKeyChecking=no root@192.168.254.128'
               //sh 'cd /opt/ansible'
               sh 'docker image build -t $JOB_NAME:v1.$BUILD_ID .'
               sh 'docker image tag $JOB_NAME:v1.$BUILD_ID thanish/$JOB_NAME:v1.$BUILD_ID'
               sh 'docker image tag $JOB_NAME:v1.$BUILD_ID thanish/$JOB_NAME:latest'
                }
            }
            }
        } */
    }
}
