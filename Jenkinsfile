pipeline{
    agent any
    stages {
        stage('Build Docker Image') {
            steps {
                sh "echo staring build the image"
                sh 'docker build -t viraj5132/bluegreen:latest .'
            }
        }
        stage('Deploy Docker Image') {
            steps {
                sh "echo staring deploy the image"
                sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                sh 'docker push viraj5132/bluegreen:latest'
            }
        }
        stage('Remove Docker Image') {
            steps {
                   sh "echo staring deploy the image"
            //       sh "docker rmi -f viraj5132/nodejsapp-1.0"  
            //       sh "ssh ubuntu@$DEPLOY_IP kubectl delete deploy nodejs-app"  
            }
        }
        stage('Deploy to Kubernetes in blue') {
              when { branch 'blue'}
            steps {
                sshagent(['3.91.222.148']) {
                    sh "echo staring deploy the image in Kubernetes"
                    sh "scp -o StrictHostKeyChecking=no nodejs.yaml ubuntu@$DEPLOY_IP:/home/ubuntu/"
                    sh "ssh ubuntu@$DEPLOY_IP kubectl apply -f nodejs.yaml" 
                }
            }
        }
        stage('Switch traffic in blue') {
              when { branch 'blue'}
            steps {
                sshagent(['3.91.222.148']) {
                    sh "echo staring switch traffic to blue"
                     sh "ssh ubuntu@$DEPLOY_IP  kubectl patch service blue-green -p '{`spec`:{`selector`:{`version`:`v1`}}}'"
                }
            }
        }
         stage('Deploy to Kubernetes in green') {
              when { branch 'green'}
            steps {
                sshagent(['3.91.222.148']) {
                    sh "echo staring switch traffic to green"
                    sh "scp -o StrictHostKeyChecking=no nodejs2.yaml ubuntu@$DEPLOY_IP:/home/ubuntu/"
                    sh "ssh ubuntu@$DEPLOY_IP kubectl apply -f nodejs2.yaml" 
                }
            }
        }
        stage('Switch traffic in green') {
              when { branch 'green'}
            steps {
                sshagent(['3.91.222.148']) {
                    sh "echo staring deploy the image in Kubernetes"
                    sh "ssh ubuntu@$DEPLOY_IP kubectl patch service blue-green -p '{`spec`:{`selector`:{`version`:`v1`}}}'"
                }
            }
        }
    
    }
}