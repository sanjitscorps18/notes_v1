pipeline{
    agent any
    
    stages{
        stage("Code"){
            steps{
                echo "Cloning the Code"
                git url:"https://github.com/sanjitscorps18/notes_v1.git", branch:"main"
            }
        }
        
        stage("Build"){
            steps{
                echo "Building the Code"
                sh "docker build -t notes-app ."
            }
            
        }
        stage("Push"){
            steps{
                echo "Pushing the Code to DockerHub"
                withCredentials([usernamePassword(credentialsId:"dockerHub",passwordVariable:"dockerPass",usernameVariable:"dockerUser")]){
			        sh "docker tag notes-app ${env.dockerUser}/notes-app:v2"
                	sh "docker login -u ${env.dockerUser} -p ${env.dockerPass}"
			        sh "docker push ${env.dockerUser}/notes-app:v2"
		        }
            }
        }
        stage("Deploy"){
            steps{
                echo "Deploying the Code"
                sh "scp -i 'jen_kp.pem' /var/lib/jenkins/workspace/new-app-18/docker-compose.yml ubuntu@ec2-54-85-158-197.compute-1.amazonaws.com:"
                sh "scp -i 'jen_kp.pem' /var/lib/jenkins/workspace/new-app-18/mynotes/build/index.html ubuntu@ec2-54-85-158-197.compute-1.amazonaws.com:"
                sshagent(credentials: ['aws_ec2']){
                    sh("ssh ubuntu@ec2-54-85-158-197.compute-1.amazonaws.com 'docker-compose stop && docker-compose pull && docker-compose up -d'")
                    //sh "docker run -d -p 8000:8000 sanjitscorps/notes-app:v1"
                }
                
            }
        }  
    }
}
