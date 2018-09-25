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
SFTP_FILE_NAME=$1
DATESTAMP=`date --date=\'today\' +"%d-%m-%Y-%H-%M-%S"`
echo $DATESTAMP
echo $SFTP_FILE_NAME

echo "##############"
echo "## Archiving previous installable if any at $DATESTAMP"
echo "##############"
cd /opt/ci/stage/downloads/
if ls /opt/ci/stage/downloads/*.zip > /dev/null 2>&1; then
        for file in *.zip; do
            mv "$file" "archive/${file%.zip}-$DATESTAMP.zip"
        done
fi

echo "##############"
echo "## Download installable from SFTP Server ##"
echo "##############"
lftp<<END_SCRIPT
open sftp://sftp.bamboorose.com
user dilip@br `echo Q29sb25lbDEh | base64 --decode`
cd "_Software/2017R1 Release & FixPacks/Tomcat"
get $SFTP_FILE_NAME
bye
END_SCRIPT

echo "##############"
echo "## Copy installable from /opt/ci/stage/downloads/ to transfer-and-extract workspace"
echo "##############"
pwd
cp -Rf *.zip /opt/ci/jenkins-slave/workspace/dcust/transfer-and-extract/'''
      }
    }
  }
}