pipeline {
  agent {
    node {
      label 'dcust-bastion'
    }

  }
  parameters {
    string(name: SFTP_FILE_PATH, defaultValue: 'BambooRoseTradeEngines-2017R1FP36-Tomcat.zip', description: 'FixPack upload location - "_Software/2017R1 Release & FixPacks/Tomcat/"')
    {
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
