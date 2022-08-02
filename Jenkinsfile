pipeline {
    agent any

    stages {
        stage('Hello') {
            steps {
                echo 'Hello World'
            }
        }
        stage('Git Cloning and Checkout') {
          steps {
            script {
               git branch: 'main', credentialsId: 'ubuntu', url: 'git@github.com:teslaluv/Demo_1_First_Try.git'
               sh "ls -lart ./*"
               sh "pwd"
               sh "ls"
               sh "git branch -a"
               sh "git checkout main"
              }
           }
        }
        stage ('Deploy') {
            steps{
                sshagent(credentials : ['ubuntu']) {
                    script {
                        sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.224.67.44 uptime'
                        sh 'ssh -v ubuntu@54.224.67.44'
                        sh 'scp -r /var/lib/jenkins/workspace/Demo_Number_1/templates ubuntu@54.224.67.44:/home/ubuntu/'
                        sh 'scp /var/lib/jenkins/workspace/Demo_Number_1/Dockerfile ubuntu@54.224.67.44:/home/ubuntu/'
                        sh 'scp /var/lib/jenkins/workspace/Demo_Number_1/app.py ubuntu@54.224.67.44:/home/ubuntu/'
                        sh 'scp /var/lib/jenkins/workspace/Demo_Number_1/requirements.txt ubuntu@54.224.67.44:/home/ubuntu/'
                        sh """ssh ubuntu@54.224.67.44 sudo docker build -t teslaluv/myfirstdemoapp ."""
                        // sh """ssh ubuntu@34.229.103.182 sudo docker run -p 80:5000 --name myfirstdemoapp teslaluv/myfirstdemoapp"""
                        try {
                                sh """ssh ubuntu@54.224.67.44 sudo docker run -d -p 80:5000 --name myfirstdemoapp teslaluv/myfirstdemoapp"""
                                }
                        catch (exc) {
                                sh """
                                  ssh ubuntu@54.224.67.44 sudo docker stop myfirstdemoapp
                                  ssh ubuntu@54.224.67.44 sudo docker rm myfirstdemoapp
                                  ssh ubuntu@54.224.67.44 sudo docker run -d -p 80:5000 --name myfirstdemoapp teslaluv/myfirstdemoapp
                                 """
                                }
                        sh 'ssh ubuntu@54.224.67.44 sudo docker ps'
                    }
                }
            }
        }
    }
}
