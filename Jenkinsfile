pipeline {
  agent {
    node {
      label 'dcust-bastion'
    }

  }
input id: '1', message: 'Migrating Fix Pack?', ok: 'Yes', parameters: [choice(choices: ['dcust', 'abc', 'mycompany', 'xyz'], description: 'Select Customer Name', name: 'CustomerName'), string(defaultValue: 'BambooRoseTradeEngines-2017R1FP36-Tomcat.zip', description: 'Description goes here', name: 'SFTP_FILE_PATH', trim: false)], submitter: 'admin', submitterParameter: 'SUBMITTER_NAME'
  stages {
    stage('Download-Artifacts') {
      steps {
        sh '''#!/bin/bash
        ##################
        # Archive existing installable and download new installable 
        # from SFTP sftp://sftp://bamboorose.com
        ##################
        SFTP_FILE_NAME=FileName
        DATESTAMP=`date --date=\'today\' +"%d-%m-%Y-%H-%M-%S"`
        echo $DATESTAMP
        echo $SFTP_FILE_NAME
        echo `hostname`'''
      }
    }
  }
}
