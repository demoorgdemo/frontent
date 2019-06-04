def release=""
def getChangeString() {
    MAX_MSG_LEN = 100
    def changeString = ""
    echo "Gathering SCM changes"
    def changeLogSets = currentBuild.rawBuild.changeSets
    echo "${changeLogSets}"
    for (int i = 0; i < changeLogSets.size(); i++) {
        def entries = changeLogSets[i].items
        for (int j = 0; j < entries.length; j++) {
            def entry = entries[j]
            echo "${changeLogSets[0].items}"
            truncated_msg = entry.msg.take(MAX_MSG_LEN)
            changeString += "  ${j+1} - ${truncated_msg} <br>"
        }
    }

    if (!changeString) {
        changeString = " - No new changes"
    }
    return changeString
}

pipeline {
	agent {
		node { label 'jnlp-slave-node' }
    	}
  	options { buildDiscarder(logRotator(numToKeepStr: '10')) }
    stages {
            stage('Checkout'){
            steps {
               slackSend channel: "#build_notifications", message: "STATUS: *Started*:woman-running:\nJob Name: *${env.JOB_NAME}* \nBUILD: *[${env.BUILD_NUMBER}]* \nURL: (${env.BUILD_URL})\nGit-Commits List -\n${getChangeString().replace('<br>', '\n')}" ,color: "#f9f904"
                echo 'Checking out code from scm...'
                checkout scm
            }
        }
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
                     slackSend channel: "#build_notifications", message: "STATUS: *Completed* :thumbsup_all:\nJob Name: *${env.JOB_NAME}* \nBUILD: *[${env.BUILD_NUMBER}]* \nURL: (${env.BUILD_URL})" ,color: "#0fc64f"
    }
}
    }
