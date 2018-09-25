pipeline {
  agent {
    node {
      label 'dcust-bastion'
    }

  }
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
        input(message: 'Fix Pack File Name :', id: 'SFTP_FILE_PATH')
        input(submitterParameter: 'Param1', submitter: 'Dilip', message: 'Enter File Name', id: 'FILE_NAME', ok: 'FILE_NAME')
      }
    }
  }
}