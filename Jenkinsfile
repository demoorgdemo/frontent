pipeline {
       environment {
     ip = "10.0.2.177"
   }
    agent any
     triggers {
       // poll repo every 1 minute for changes
       pollSCM('0 1,7 * * *')
   }
       parameters {
string(name: 'JavaVersion', defaultValue: '8.4.0', description: 'Java compatibility for building Demo App!!', trim: true)
choice(choices: '2\n1\n3\n4\n5', description: 'Application-Instance', name: 'ApplicationInstance')
}
    stages { 
        stage('Health Check') {
            steps {
        timeout(time: 2, unit: 'MINUTES') {
                    sh  '''
                    bash -c 'while [[ "$(curl -s -o /dev/null -w ''%{http_code}'' https://www.google.com)" != "200" ]]; do sleep 5 ;echo "Waiting for Health Status ........"; done'
                    '''
                }
            }
        } 
    }
    post {
    success {
          sh ' echo slack notification and email'
    }
}
    }
