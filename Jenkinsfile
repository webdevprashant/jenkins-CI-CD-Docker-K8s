pipeline {
    
    agent {
        label "linuxbuildnode"
    }
    
    
    stages {
        stage('SCM') {
            steps {
                git 'https://github.com/webdevprashant/jenkins-CI-CD-Docker-K8s.git'
                
            }
            
        }
        
        stage('Build by Maven Package') {
            steps {
                sh 'mvn clean package'
            }
            
        }
        
        
        stage('Build Docker OWN image') {
            steps {
                sh "sudo docker build -t  webdevprashant/javawebday7:${BUILD_TAG}  ."
                //sh 'whoami'
            }
            
        }

        stage('Push Image to Docker HUB') {
            steps {
                
                withCredentials([string(credentialsId: 'VAR_FOR_DOCKERPASS', variable: 'DOCKER_HUB_PASS_CODE')]) {
                 sh "sudo docker login -u webdevprashant -p $VAR_FOR_DOCKERPASS"
                    }
               sh "sudo docker push webdevprashant/javawebday7:${BUILD_TAG}"
            }
        }
        
        stage('Deploy webAPP in DEV Env') {
            steps {
                sh 'sudo docker rm -f myjavaapp'
//                 sh "sudo docker run  -d  -p  8080:8080 --name myjavaapp   vimal13/javaweb:${BUILD_TAG}"
                sh "sudo docker run  -d  -p  1220:8080 --name myjavaapp   webdevprashant/javawebday7:${BUILD_TAG}"
                //sh 'whoami'
            }
            
        }
        
        stage('Deploy webAPP in QA/Test Env') {
            steps {
               
               sshagent(['QA_ENV_SSH_CRED']) {
    
//                     sh "ssh  -o  StrictHostKeyChecking=no ec2-user@13.233.100.238 sudo docker rm -f myjavaapp"
//                     sh "ssh ec2-user@13.233.100.238 sudo docker run  -d  -p  8080:8080 --name myjavaapp   vimal13/javaweb:${BUILD_TAG}"
                   sh "sudo docker run  -d  -p  8080:8080 webdevprashant/javawebday7:${BUILD_TAG}"
                }
            }
        }
        
         stage('QAT Test') {
            steps {        
               // sh 'curl --silent http://13.233.100.238:8080/java-web-app/ |  grep India'
                retry(30) {
                    sh 'curl --silent http://13.233.100.238:8080/java-web-app/ |  grep India'
                }
            }
        }
          
        stage('approved') {
            steps {
            script {
                Boolean userInput = input(id: 'Proceed1', message: 'Promote build?', parameters: [[$class: 'BooleanParameterDefinition', defaultValue: true, description: '', name: 'Please confirm you agree with this']])
                echo 'userInput: ' + userInput

                if(userInput == true) {
                    // do action
                } else {
                    // not do action
                    echo "Action was aborted."
                }      
            }
        }
        }
        
        stage('Deploy webAPP in Prod Env') {
            steps {
               agent { label 'windowsslave' }
                sh 'kubectl get pods'
//                sshagent(['QA_ENV_SSH_CRED']) { 
//                     sh "ssh  -o  StrictHostKeyChecking=no ec2-user@13.232.250.244 sudo kubectl  delete    deployment myjavawebapp"
//                     sh "ssh  ec2-user@13.232.250.244 sudo kubectl  create    deployment myjavawebapp  --image=vimal13/javaweb:${BUILD_TAG}"
//                     sh "ssh ec2-user@13.232.250.244 sudo wget https://raw.githubusercontent.com/vimallinuxworld13/jenkins-docker-maven-java-webapp/master/webappsvc.yml"
//                     sh "ssh ec2-user@13.232.250.244 sudo kubectl  apply -f webappsvc.yml"
//                     sh "ssh ec2-user@13.232.250.244 sudo kubectl  scale deployment myjavawebapp --replicas=5"
//                 }
            }  
        } 
    }
    
     post {
         always {
             echo "You can always see me"
         }
         success {
              echo "I am running because the job ran successfully"
         }
         unstable {
              echo "Gear up ! The build is unstable. Try fix it"
         }
         failure {
             echo "OMG ! The build failed"
             mail bcc: '', body: 'hi check this ..', cc: '', from: '', replyTo: '', subject: 'job ete fail', to: 'vdaga@lwindia.com'
         }
     }
}
