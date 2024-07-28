pipeline{
    agent any

    environment {
        PATH = "$PATH:/usr/local/bin"
    }
    
    stages{
        stage('build'){
            steps{
                script{
                    sh 'docker build -t java-app .'
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
                     withAWS(credentials: 'aws-cli', region: 'us-east-1'){
                    sh 'aws eks update-kubeconfig --region us-east-1 --name java-app'
                    sh 'kubectl apply -f ./k8s/deployment.yaml'
                    sh 'kubectl expose deployment school-website --type=NodePort --port=80 '
                     }
                    
                }
            }
        }
    }
}
