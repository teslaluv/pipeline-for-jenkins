pipeline {
    agent any
    
    environment {
		DOCKERHUB_CREDENTIALS=credentials('DOCKERHUB')
	}

    stages {
        stage('Git Cloning and Checkout') {
          steps {
            script {
               git branch: 'main', credentialsId: 'ubuntu', url: 'git@github.com:teslaluv/Demo_1_First_Try.git'
               sh "ls -lart ./*"
               sh "pwd"
               sh "ls"
               sh "git branch -a"
               sh "git checkout main"
               GIT_COMMIT_HASH = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)
               sh "git log -n 1 --pretty=format:'%H'"
              }
           }
        }
        stage('Building image'){
            steps {
                script {
                    sh """docker build -t teslaluv/myfirstdemoapp:${GIT_COMMIT_HASH}.${BUILD_NUMBER} ."""
                }
            }
        }
	    stage('Push to Dockerhub'){
           steps{
             dir('source') {
               sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
               sh """docker push teslaluv/myfirstdemoapp:${GIT_COMMIT_HASH}.${BUILD_NUMBER}"""
             }
	       }
	    }
        stage ('Deploy') {
            steps{
                sshagent(credentials : ['ubuntu']) {
                    script {
                        sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.224.67.44 uptime'
                        sh 'ssh -v ubuntu@54.224.67.44'
                        sh """ssh ubuntu@54.224.67.44 sudo docker pull teslaluv/myfirstdemoapp:${GIT_COMMIT_HASH}.${BUILD_NUMBER}"""
                        try {
                                sh """ssh ubuntu@54.224.67.44 sudo docker run -d -p 80:5000 --name myfirstdemoapp teslaluv/myfirstdemoapp:${GIT_COMMIT_HASH}.${BUILD_NUMBER}"""
                                }
                        catch (exc) {
                                sh """
                                  ssh ubuntu@54.224.67.44 sudo docker stop myfirstdemoapp
                                  ssh ubuntu@54.224.67.44 sudo docker rm myfirstdemoapp
                                  ssh ubuntu@54.224.67.44 sudo docker run -d -p 80:5000 --name myfirstdemoapp teslaluv/myfirstdemoapp:${GIT_COMMIT_HASH}.${BUILD_NUMBER}
                                 """
                                }
                        sh 'ssh ubuntu@54.224.67.44 sudo docker ps'
                    }
                }
            }
        }
    }
}
