pipeline {
  agent {
    node {
      label 'dcust-bastion'
    }

  }
  stages {
    stage('Download Artifact') {
      environment {
        SFTP_FILE_PATH = "${params.SFTP_FILE_PATH}"
      }
      steps {
        echo "Customer Name: ${params.CUST_NAME}"
        echo "Artifact Filename: ${params.SFTP_FILE_PATH}"
        echo "Start Job?: ${params.START_JOB}"
        sh '''
        #!/bin/bash -xe
        ##################
        # Archive existing installable and download new installable 
        # from SFTP sftp://sftp://bamboorose.com
        ##################
        # SFTP_FILE_NAME=$1
        DATESTAMP=`date --date=\'today\' +"%d-%m-%Y-%H-%M-%S"`
        echo "DATESTAMP :"$DATESTAMP
        SFTP_FILE_NAME=${SFTP_FILE_PATH}
        echo "File Name :"${SFTP_FILE_PATH}

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
        bye
END_SCRIPT
        # Comment out below line to download actual file &
        # add below line at the end of FTP (above bye statement)
        # get $SFTP_FILE_NAME
        touch /opt/ci/stage/downloads/$SFTP_FILE_NAME        


        echo "##############"
        echo "## Copy installable from /opt/ci/stage/downloads/ to transfer-and-extract workspace"
        echo "##############"
        pwd
        cp -Rf *.zip /opt/ci/jenkins-slave/workspace/dcust/transfer-and-extract/
        '''
      }
    }
    stage('Transfer & Extract') {
      steps {
        sh 'echo "Transferring artifacts to APP, APP-INT and DB Servers"'
      }
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
}