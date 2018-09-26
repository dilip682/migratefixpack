pipeline {
  agent {
    node {
      label 'dcust-bastion'
    }

  }
  stages {
    stage('Download Artifact') {
      steps {
        echo 'Enter File Name '
        echo "Trying: ${params.CUST_NAME}"
        echo "We can dance: ${params.SFTP_FILE_PATH}"
        echo "The DJ says: ${params.sTrAnGePaRaM}"
      }
    }
  }
  parameters {
    choice(name: 'CUST_NAME', choices: '''dcust
abc
xyz
aaa''', description: 'Enter Customer name ?')
    booleanParam(name: 'SFTP_FILE_PATH', defaultValue: true, description: 'Checkbox parameter')
    string(name: 'sTrAnGePaRaM', defaultValue: 'Dance!', description: 'Do the funky chicken!')
  }
}
