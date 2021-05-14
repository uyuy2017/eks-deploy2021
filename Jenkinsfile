pipeline {
  agent any
  stages {
   
      stage('build docker image') {
      steps {
        withCredentials(bindings: [[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]) {
          sh '''
              docker build -t uyuy2015/eks-blue-greendeployment:test2 .
             '''
        }

      }
    }

    stage('push docker image to dockerhub repository') {
      steps {
        withCredentials(bindings: [[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]) {
          sh '''
               docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD
               docker push uyuy2015/eks-blue-greendeployment:test2 
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
