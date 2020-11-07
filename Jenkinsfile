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
                  sh 'docker build -t cloud-devops-nanodegree-capstone .'
              }
         }
         stage('Push Docker Image') {
              steps {
                  withDockerRegistry([url: "", credentialsId: "docker-hub"]) {
                      sh "docker tag cloud-devops-nanodegree-capstone shoumaw/cloud-devops-nanodegree-capstone"
                      sh 'docker push shoumaw/cloud-devops-nanodegree-capstone'
                  }
              }
         }
         stage('Deploying') {
              steps{
                  echo 'Deploying to AWS...'
                  withAWS(credentials: 'aws', region: 'us-east-2') {
                      sh "aws eks --region us-east-2 update-kubeconfig --name cluster"
                      sh "kubectl config use-context arn:aws:eks:us-east-2:519248592653:cluster/cluster"
                      sh "kubectl set image deployments/cloud-devops-nanodegree-capstone cloud-devops-nanodegree-capstone=shoumaw/cloud-devops-nanodegree-capstone:latest"
                      sh "kubectl apply -f deployment/deployment.yml"
                      sh "kubectl get nodes"
                      sh "kubectl get deployment"
                      sh "kubectl get pod -o wide"
                      sh "kubectl get service/cloud-devops-nanodegree-capstone"
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
