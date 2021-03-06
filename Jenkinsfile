pipeline {
     agent any
     stages {
         stage('Build') {
             steps {
                 sh 'echo "Building"'
             }
         }
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
         stage('Build Docker Image') {
              steps {
                  sh 'docker build -t capstone-project-cloud-devops .'
              }
         }
         stage('Push Docker Image') {
             steps {
                withDockerRegistry([url: "", credentialsId: "docker-hub"]) {
                      sh "docker tag capstone-project-cloud-devops crisroddev/capstone-project-cloud-devops"
                      sh 'docker push crisroddev/capstone-project-cloud-devops'
             }
            }
         }
         stage('Deploying') {
             steps {
                 echo 'Deploying to AWS'
                  withAWS(credentials: 'aws', region: 'us-east-1') {
                     sh 'echo "Uploading to AWS"'
                     s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html',  bucket:'jenkins-casptone-udacity')
                      sh "aws eks --region us-east-1 update-kubeconfig --name EKSCluster-Fej9knGAYnfE"
                      sh "kubectl config use-context arn:aws:eks:us-east-1:438569415701:cluster/EKSCluster-Fej9knGAYnfE"
                      sh "kubectl apply -f Deployment/deployment.yml"
                      sh "kubectl get nodes"
                      sh "kubectl get deployment"
                      sh "kubectl get pod -o wide"
                      sh "kubectl get service/capstone-project-cloud-devops"
                      sh "kubectl set image deployments/capstone-project-cloud-devops capstone-project-cloud-devops=crisroddev/capstone-project-cloud-devops:latest"
                 }
             }
         }
         stage ('Cleaning') {
             steps {
                 echo 'Cleaning...'
                 sh "docker system prune -af"
             }
         }
    }
}         