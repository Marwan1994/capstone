pipeline {
     agent any
     stages {
         stage('Build') {
             steps {
                 sh 'echo "Hello World"'
                 sh '''
                     echo "Multiline shell steps works too"
                     ls -lah
                 '''
             }
         }
         stage('Lint HTML') {
              steps {
                  sh 'tidy -q -e *.html'
              }
         }
        /*stage('Security Scan') {
              steps { 
                 aquaMicroscanner imageName: 'alpine:latest', notCompleted: 'exit 1', onDisallowed: 'fail'
              }
         }         */
         /*stage('Upload to AWS') {
              steps {
                  withAWS(region:'us-east-2',credentials:'capstone') {
                  sh 'echo "Uploading content with AWS creds"'
                      s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'udacity-proj03')
                  }
              }*/
            stage('Build image') {
                steps {
                    sh 'sudo docker build -t capstone-proj .'
                }
            }
            stage('Push image to Repo'){
                steps {
                    withDockerRegistry([url: '', credentialsId: '']) {
                        sh 'sudo docker login -u "marwanabdelhafez" -p "FEzo1866?" '
                        sh 'sudo docker tag capstone-proj marwanabdelhafez/capstone-proj'
                        sh 'sudo docker push marwanabdelhafez/capstone-proj:latest'
                    }
                }
            }
            stage('Create K8s cluster') {
                steps {
                    sh 'sudo echo creating EKS cluster'
                    withAWS(credentials: '', region: '') {
                    sh 'sudo eksctl create cluster --name capstone-proj-cluster  --region us-east-2 --nodegroup-name capstone-proj-nodes --nodes 2 --nodes-min 1 --nodes-max 3 --managed'
                    }
                }
            }
            stage('Generate kubeconfig') {
                steps {
                    withAWS(credentials: 'aws', region: 'us-east-2') {
                    sh 'sudo aws eks --region us-east-2 update-kubeconfig --name capstone-proj-cluster'
                 } 
                }
            }
            stage('Rollout deployment') {
                steps {
                    withAWS(credentials: 'aws', region: 'us-east-2') {
                    sh 'sudo kubectl apply -f deployment.yml'
                    } 
                }
            }
            stage('Check Deployment') {
                steps {
                    withAWS(credentials: 'aws', region: 'us-east-2') {
                    sh 'sudo kubectl get nodes'
                    sh 'sudo kubectl get deployment'
                    sh 'sudo kubectl get pod'
                    sh 'sudo kubectl get service/capstone-LB-service'
                    } 
                }
            }
            stage('Purge system') {
                steps {
                echo 'sudo purge system'
                sh 'sudo docker system prune'
                sh 'sudo eksctl delete cluster --cluster=capstone-proj-cluster'
                sh 'sudo eksctl delete nodegroup --cluster=capstone-proj-cluster --name=capstone-proj-nodes'
                sh 'sudo aws cloudformation delete-stack --stack-name eksctl-capstone-proj-cluster-nodegroup-capstone-proj-nodes'
                sh 'sudo aws cloudformation delete-stack --stack-name eksctl-capstone-proj-cluster-cluster'
             }
            }   
    }
}

