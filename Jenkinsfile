pipeline {
  agent any
  stages {
   
    stage('build docker image') {
      steps {
        withCredentials(bindings: [[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]) {
          sh '''
              sudo docker build -t uyuy2015/eks-blue-greendeployment .
             '''
        }

      }
    }

    stage('push docker image to dockerhub repository') {
      steps {
        withCredentials([usernameColonPassword(credentialsId: 'dockerhub', variable: 'dockerhub')]) {
          sh '''
              sudo -u $dockerhub docker push uyuy2015/eks-blue-greendeployment 
               '''
        }

      }
    }
    
  stage('create kubecontext file') {
      steps {
        withAWS(region: 'us-east-2', credentials: 'MyCredentials') {
          sh '''
                      aws eks update-kubeconfig --name jenkinstest2
                   '''
        }

      }
    }

    stage('Set current kubectl context') {
      steps {
        withAWS(region: 'us-east-2', credentials: 'MyCredentials') {
          sh '''
                      kubectl config use-context arn:aws:eks:us-east-2:332819193662:cluster/jenkinstest2
                   '''
        }

      }
    }

    stage('create replication controller for blue app') {
      steps {
        withAWS(region: 'us-east-2', credentials: 'MyCredentials') {
          sh '''
                      kubectl apply -f ./blue-replication-controller.yaml
                   '''
        }

      }
    }

    stage('create replication controller for green app') {
      steps {
        withAWS(region: 'us-east-2', credentials: 'MyCredentials') {
          sh '''
                      kubectl apply -f ./green-replication-controller.yaml
                   '''
        }

      }
    }

    stage('create service for blue app and make loadbalancer point to it') {
      steps {
        withAWS(region: 'us-east-2', credentials: 'MyCredentials') {
          sh '''
                      kubectl apply -f ./blue-service.yaml
                   '''
        }

      }
    }

    stage('Sanity check') {
      steps {
        input 'Does the staging environment look ok ?'
      }
    }

    stage('create service for green app and make loadbalancer point to it') {
      steps {
        withAWS(region: 'us-east-2', credentials: 'MyCredentials') {
          sh '''
                      kubectl apply -f ./green-service.yaml
                   '''
        }

      }
    }

  }
}
