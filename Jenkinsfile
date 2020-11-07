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
        /* stage('Security Scan') {
              steps { 
                 aquaMicroscanner imageName: 'alpine:latest', notCompleted: 'exit 1', onDisallowed: 'fail'
              }
         }     */    
         /*stage('Upload to AWS') {
              steps {
                  withAWS(region:'us-east-2',credentials:'aws') {
                  sh 'echo "Uploading content with AWS creds"'
                      s3Upload(pathStyleAccessEnabled: true, payloadSigningEnabled: true, file:'index.html', bucket:'udacity-proj03')
                  }
              }*/
            stage('Build image') {
                steps {
                    sh 'docker build -t capstone-proj .'
                }
            }
            stage('Push image to Repo'){
                steps {
                    withDockerRegistry([url: '', credentialsId: 'dockerhub-cred']) {
                        sh 'docker tag capstone-proj elnaggar3012/capstone-proj'
                        sh 'docker push elnaggar3012/capstone-proj'
                    }
                }
            }
            stage('Create K8s cluster') {
                steps {
                    sh 'echo creating EKS cluster'
                    withAWS(credentials: 'aws', region: 'us-east-2') {
                    sh 'eksctl create cluster --name capstone-proj-cluster  --region us-east-2 --nodegroup-name capstone-proj-nodes --nodes 2 --nodes-min 1 --nodes-max 3 --managed'
                    }
                }
            }
            stage('Generate kubeconfig') {
                steps {
                    withAWS(credentials: 'aws', region: 'us-east-2') {
                    sh 'aws eks --region us-east-2 update-kubeconfig --name capstone-proj-cluster'
                 } 
                }
            }
            stage('Rollout deployment') {
                steps {
                    withAWS(credentials: 'aws', region: 'us-east-2') {
                    sh 'kubectl apply -f deployment.yml'
                    } 
                }
            }
            stage('Check Deployment') {
                steps {
                    withAWS(credentials: 'aws', region: 'us-east-2') {
                    sh 'kubectl get nodes'
                    sh 'kubectl get deployment'
                    sh 'kubectl get pod'
                    sh 'kubectl get service/capstone-LB-service'
                    } 
                }
            }
            stage('Purge system') {
                steps {
                echo 'purge system'
                sh 'docker system prune'
                sh 'eksctl delete cluster --cluster=capstone-proj-cluster'
                sh 'eksctl delete nodegroup --cluster=capstone-proj-cluster --name=capstone-proj-nodes'
                sh 'aws cloudformation delete-stack --stack-name eksctl-capstone-proj-cluster-nodegroup-capstone-proj-nodes'
                sh 'aws cloudformation delete-stack --stack-name eksctl-capstone-proj-cluster-cluster'
             }
            }   
    }
}
