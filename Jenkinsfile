pipeline {
  agent any
  stages {
       
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
