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
        WORKSPACE=`pwd`
        echo "WORKSPACE - "$WORKSPACE

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
        # touch test.txt
        # zip /opt/ci/stage/downloads/$SFTP_FILE_NAME test.txt
        cp /opt/ci/stage/TE-Software/$SFTP_FILE_NAME .

        echo "##############"
        echo "## Copy installable from /opt/ci/stage/downloads/ to transfer-and-extract workspace"
        echo "##############"
        pwd
        echo "Zip file copied to ${WORKSPACE}"
        cp -Rf *.zip $WORKSPACE
        '''
      }
    }
    stage('Transfer & Extract') {
      parallel {
        stage('Transfer & Extract') {
          steps {
            sh 'echo "Transferring artifacts to APP, APP-INT and DB Servers"'
          }
        }
        stage('transfer to dcust-test01') {
          steps {
            echo 'Transfer to dcust-test01 box'
            sshPublisher(publishers: [sshPublisherDesc(configName: 'dcust-test01', transfers: [sshTransfer(excludes: '', execCommand: '''#!/bin/bash -ex
            ##################
            # Transfer installable from Bastion host to App and DB Servers &
            # unzip installable at stage location /opt/ci/migrations/
            ##################
            DATESTAMP=`date --date=\'today\' +"%d-%m-%Y-%H-%M-%S"`
            MIG_FOLDER=mig-`date --date=\'today\' +"%d-%m-%Y"`

            echo "##############"
            echo "## Create initial folders if it\'s first time migration "
            echo "##############"
            if [ ! -d /opt/ci/migrations/archive ]; then
                mkdir -p /opt/ci/migrations/archive;
            fi;

            echo "##############"
            echo "## Archiving previous installable if any at $DATESTAMP"
            echo "##############"
            cd /opt/ci/migrations/
            ## Archive zip files
            if ls /opt/ci/migrations/*.zip > /dev/null 2>&1; then
                    for file in *.zip; do
                        mv "$file" "archive/${file%.zip}-$DATESTAMP.zip"
                    done
            fi

            ## Archive folders
            for D in mig*; do
                if [ -d "${D}" ]; then
                        echo "${D}"
                        mv "${D}" "archive/${D%.zip}-$DATESTAMP"
                        echo  "${D}" "archive/${D%.zip}-$DATESTAMP"
                fi
            done

            echo "## Copy installable to /usr/share/client_folders/"
            cd /usr/share/client_folders/
            mkdir -p dev/$MIG_FOLDER
            cp /opt/ci/jenkins-slave/migrations/*.zip  dev/$MIG_FOLDER/

            echo "## Moving installable to stage location /opt/ci/migrations/"
            cd /opt/ci/migrations/
            mkdir $MIG_FOLDER
            cd /opt/ci/migrations/$MIG_FOLDER
            cp /opt/ci/jenkins-slave/migrations/*.zip  .
            echo "Presentl folder - `pwd`"
            unzip -o *.zip

            echo "## Remove ${params.SFTP_FILE_PATH}"
            rm -Rf ${params.SFTP_FILE_PATH}
            touch ${params.SFTP_FILE_PATH}

            ''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'migrations/', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.zip')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
          }
        }
        stage('transfer to dcust-testint') {
          environment {
            WORKSPACE_PATH = pwd()
          }
          steps {
            echo 'Transfer to dcust-testint box'
            sshPublisher(publishers: [sshPublisherDesc(configName: 'dcust-testint', transfers: [sshTransfer(excludes: '', execCommand: '''#!/bin/bash -ex
            ##################
            # Transfer installable from Bastion host to App and DB Servers &
            # unzip installable at stage location /opt/ci/migrations/
            ##################
            DATESTAMP=`date --date=\'today\' +"%d-%m-%Y-%H-%M-%S"`
            MIG_FOLDER=mig-`date --date=\'today\' +"%d-%m-%Y"`

            echo "##############"
            echo "## Create initial folders if it\'s first time migration "
            echo "##############"
            if [ ! -d /opt/ci/migrations/archive ]; then
                mkdir -p /opt/ci/migrations/archive;
            fi;

            echo "##############"
            echo "## Archiving previous installable if any at $DATESTAMP"
            echo "##############"
            cd /opt/ci/migrations/
            ## Archive zip files
            if ls /opt/ci/migrations/*.zip > /dev/null 2>&1; then
                    for file in *.zip; do
                        mv "$file" "archive/${file%.zip}-$DATESTAMP.zip"
                    done
            fi

            ## Archive folders
            for D in mig*; do
                if [ -d "${D}" ]; then
                        echo "${D}"
                        mv "${D}" "archive/${D%.zip}-$DATESTAMP"
                        echo  "${D}" "archive/${D%.zip}-$DATESTAMP"
                fi
            done

            echo "## Moving installable to stage location /opt/ci/migrations/"
            mkdir $MIG_FOLDER
            cd /opt/ci/migrations/$MIG_FOLDER
            mv /opt/ci/jenkins-slave/migrations/*.zip  .
            echo "Presentl folder - `pwd`"
            unzip -o *.zip

            echo "## Remove ${params.SFTP_FILE_PATH}"
            rm -Rf ${params.SFTP_FILE_PATH}
            touch ${params.SFTP_FILE_PATH}

            ''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'migrations/', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.zip')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
          }
        }
        stage('transfer to dcust-test-Oracle-Tools') {
          steps {
            echo 'Transfer to dcust-test-Oracle-Tools box'
            sshPublisher(publishers: [sshPublisherDesc(configName: 'dcust-test-Oracle-Tools', transfers: [sshTransfer(excludes: '', execCommand: '''#!/bin/bash -ex
            ##################
            # Transfer installable from Bastion host to App and DB Servers &
            # unzip installable at stage location /opt/ci/migrations/
            ##################
            DATESTAMP=`date --date=\'today\' +"%d-%m-%Y-%H-%M-%S"`
            MIG_FOLDER=mig-`date --date=\'today\' +"%d-%m-%Y"`

            echo "##############"
            echo "## Create initial folders if it\'s first time migration "
            echo "##############"
            if [ ! -d /opt/ci/migrations/archive ]; then
                mkdir -p /opt/ci/migrations/archive;
            fi;

            echo "##############"
            echo "## Archiving previous installable if any at $DATESTAMP"
            echo "##############"
            cd /opt/ci/migrations/
            ## Archive zip files
            if ls /opt/ci/migrations/*.zip > /dev/null 2>&1; then
                    for file in *.zip; do
                        mv "$file" "archive/${file%.zip}-$DATESTAMP.zip"
                    done
            fi

            ## Archive folders
            for D in mig*; do
                if [ -d "${D}" ]; then
                        echo "${D}"
                        mv "${D}" "archive/${D%.zip}-$DATESTAMP"
                        echo  "${D}" "archive/${D%.zip}-$DATESTAMP"
                fi
            done

            echo "## Moving installable to stage location /opt/ci/migrations/"
            mkdir $MIG_FOLDER
            cd /opt/ci/migrations/$MIG_FOLDER
            mv /opt/ci/jenkins-slave/migrations/*.zip  .
            echo "Presentl folder - `pwd`"
            unzip -o *.zip

            echo "## Remove ${params.SFTP_FILE_PATH}"
            rm -Rf ${params.SFTP_FILE_PATH}
            touch ${params.SFTP_FILE_PATH}

            ''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'migrations/', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '*.zip')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
          }
        }
      }
    }
    stage('Upgrade Applications') {

      parallel {
        stage('Upgrade Trade Engine') {
          steps {
            echo 'Upgrading Trade Engine'
            sshPublisher(publishers: [sshPublisherDesc(configName: 'dcust-test01', transfers: [sshTransfer(excludes: '', execCommand: '''#!/bin/bash -ex
            echo "## Set Env"
            DATESTAMP=`date --date=\'today\' +"%d-%m-%Y-%H-%M-%S"`
            MIG_FOLDER=mig-`date --date=\'today\' +"%d-%m-%Y"`
            source /etc/tomcat/tomcat.conf

            ##################
            # Shutdown Tomcat
            ##################
            sudo systemctl stop tomcat

            ##################
            # Backup
            ##################
            echo "## Backup old .war to /opt/ci/migrations/$MIG_FOLDER/backup"
            sudo mkdir -p /opt/ci/migrations/$MIG_FOLDER/backup
            sudo cp -Rf $CATALINA_HOME/webapps/dev.war /opt/ci/migrations/$MIG_FOLDER/backup/dev-$DATESTAMP.war

            echo "## Clear Cache"
            cd $CATALINA_HOME/webapps
            sudo rm -Rf dev.war
            sudo rm -Rf dev
            sudo rm -Rf $CATALINA_HOME/work/Catalina/localhost/dev

            echo "## copy .war to $CATALINA_HOME/webapps"
            sudo cp -Rf /opt/ci/migrations/$MIG_FOLDER/*Tomcat.war $CATALINA_HOME/webapps/dev.war
            ''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
          }
        }
      }
    }
    stage('Upgrade DB') {
      stage('Backup DB') {
        steps {
          echo 'Backing up old DB'
          sshPublisher(publishers: [sshPublisherDesc(configName: 'dcust-test-Oracle-Tools', transfers: [sshTransfer(excludes: '', execCommand: '''#!/bin/bash -ex
          echo "## Set Env"
          DATESTAMP=`date --date=\'today\' +"%d-%m-%Y-%H-%M-%S"`
          MIG_FOLDER=mig-`date --date=\'today\' +"%d-%m-%Y"`

          ##################
          # Backup DB
          ##################
          cd /opt/ci/migrations/$MIG_FOLDER/
          sudo chmod 755 deploydb.properties
          exp tssdemo/stone01@DEV file=DCUST.dmp log=DCUST_DEV.log
          tail DCUST_DEV.log
          echo "## Backup complete"
          ''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
        }
      }
        stage('Pre-Upgrade config') {
          steps {
            echo 'Pre-Upgrade config'
            sshPublisher(publishers: [sshPublisherDesc(configName: 'dcust-test-Oracle-Tools', transfers: [sshTransfer(excludes: '', execCommand: '''#!/bin/bash -ex
            echo "## Set Env"
            DATESTAMP=`date --date=\'today\' +"%d-%m-%Y-%H-%M-%S"`
            MIG_FOLDER=mig-`date --date=\'today\' +"%d-%m-%Y"`

            ##################
            # Update deploydb.properties file
            ##################
            cd /opt/ci/migrations/$MIG_FOLDER/
            sudo cp -Rf /opt/ci/stage/customers/dcust/dev/deploydb-dcust-dev.properties deploydb.properties
            ''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
          }
        }
        stage('Upgrade DB') {
          steps {
            sshPublisher(publishers: [sshPublisherDesc(configName: 'dcust-test-Oracle-Tools', transfers: [sshTransfer(excludes: '', execCommand: '''#!/bin/bash -ex
            echo "## Set Env"
            DATESTAMP=`date --date=\'today\' +"%d-%m-%Y-%H-%M-%S"`
            MIG_FOLDER=mig-`date --date=\'today\' +"%d-%m-%Y"`

            ##################
            # Update deploydb.properties file
            ##################
            cd /opt/ci/migrations/$MIG_FOLDER/
            sh install.sh upgrade_oracle
            echo "## install.sh complete"
            ''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
          }
        }
    }
    stage('Post upgrade Config') {
      parallel {
        stage('Post upgrade Config') {
          steps {
            echo 'Performing post upgrade config'
          }
        }

        stage('Change Dashboard msg') {
          steps {
            echo 'Changing Dashboard msg'
          }
        }
      }
    }
    stage('Start Tomcat Instances') {
      steps {
        echo 'Starting Tomcat Instances'
        sshPublisher(publishers: [sshPublisherDesc(configName: 'dcust-test01', transfers: [sshTransfer(excludes: '', execCommand: '''#!/bin/bash -ex

        ##################
        # Start Tomcat
        ##################
        sudo systemctl start tomcat

        ##################
        # Test Home Page
        ##################
        echo "## Test Home Page"
        curl "http://dcust-elb-398506934.ap-northeast-1.elb.amazonaws.com/dev/tss.jsp"
        ''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
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
