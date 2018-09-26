pipeline {
  agent {
    node {
      label 'dcust-bastion'
    }
  }
  parameters {
    choice(name: 'CUST_NAME', choices: '''dcust
abc
xyz
aaa''', description: 'Enter Customer name ?')
string(name: 'SFTP_FILE_PATH', defaultValue: 'BambooRoseTradeEngines-2017R1FP36-Tomcat.zip', description: 'Artifact to be downloaded')
    booleanParam(name: 'START_JOB', defaultValue: true, description: 'Checkbox parameter')
  }

  stages {
    stage('Download Artifact') {
      steps {
        echo "Customer Name: ${params.CUST_NAME}"
        echo "Artifact Filename: ${params.SFTP_FILE_PATH}"
        echo "Start Job?: ${params.START_JOB}"
      }
    }
  }
}
