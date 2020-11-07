pipeline {
     agent any
     stages {
         stage('Build') {
              steps {
                  sh 'echo Building...'
              }
         }
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
         stage('Build Docker Image') {
              steps {
                  sh 'sudo docker build -t udacity-capstone .'
              }
         }
         stage('Push Docker Image') {
              steps {
                  withDockerRegistry([url: "", credentialsId: "docker-id"]) {
                      sh "sudo docker tag udacity-capstone alishouman/udacity-capstone"
                      sh 'sudo docker push alishouman/udacity-capstone:latest'
                  }
              }
         }
         stage('Deploying') {
              steps{
                  echo 'Deploying to AWS...'
                  withAWS(credentials: 'aws-id', region: 'us-east-2') {
                      sh "aws eks --region us-east-2 update-kubeconfig --name cluster"
                      sh "kubectl config use-context arn:aws:eks:us-east-2:519248592653:cluster/cluster"
                      sh "kubectl set image deployments/udacity-capstone udacity-capstone=alishouman/udacity-capstone:latest"
                      sh "kubectl apply -f deployment/deployment.yml"
                      sh "kubectl get nodes"
                      sh "kubectl get deployment"
                      sh "kubectl get pod -o wide"
                      sh "kubectl get service/udacity-capstone"
                  }
              }
        }
        stage("Cleaning up") {
              steps{
                    echo 'Cleaning up...'
                    sh "docker system prune"
              }
        }
     }
}
