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
                  sh 'docker build -t Cloud-Devops-Nanodegree-Capstone .'
              }
         }
         stage('Push Docker Image') {
              steps {
                  withDockerRegistry([url: "", credentialsId: "docker-hub"]) {
                      sh "docker tag Cloud-Devops-Nanodegree-Capstone shoumaw/Cloud-Devops-Nanodegree-Capstone"
                      sh 'docker push shoumaw/Cloud-Devops-Nanodegree-Capstone'
                  }
              }
         }
         stage('Deploying') {
              steps{
                  echo 'Deploying to AWS...'
                  withAWS(credentials: 'aws', region: 'us-east-2') {
                      sh "aws eks --region us-east-2 update-kubeconfig --name cluster"
                      sh "kubectl config use-context arn:aws:eks:us-east-2:519248592653:cluster/cluster"
                      sh "kubectl set image deployments/Cloud-Devops-Nanodegree-Capstone Cloud-Devops-Nanodegree-Capstone=shoumaw/Cloud-Devops-Nanodegree-Capstone:latest"
                      sh "kubectl apply -f deployment/deployment.yml"
                      sh "kubectl get nodes"
                      sh "kubectl get deployment"
                      sh "kubectl get pod -o wide"
                      sh "kubectl get service/Cloud-Devops-Nanodegree-Capstone"
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
