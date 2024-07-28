pipeline{
    agent any
    
    stages{
        stage('build'){
            steps{
                script{
                    sh 'sudo docker build -t java-app .'
                }
            }
        }

        stage('push'){
            steps{
                script{
                    withCredentials([usernamePassword(credentialsId: 'Docker-hub', passwordVariable: 'Password', usernameVariable: 'Username')]) {
                    sh 'docker login --username $Username --password $Password'
                    sh 'docker tag java-app $Username/java-app'
                    sh 'docker push $Username/java-app'
                    }
                }
            }
        }

        stage('deploy'){
            steps{
                script{
                    sh 'aws eks update-kubeconfig --region us-east-1 --name java-app'
                    sh 'kubectl apply -f ./k8s/deployment.yaml'
                    sh 'kubectl expose deployment java-app --type=NodePort --port=80 --node-port=30000'
                    
                }
            }
        }
    }
}
