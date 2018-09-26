pipeline {
  agent none
  stages {
    stage('User Input') {
      steps {
        input 'Enter Customer Name'
      }
    }
  }
  parameters {
    string(name: 'SFTP_FILE_PATH', defaultValue: 'BambooRoseTradeEngines-2017R1FP36-Tomcat.zip', description: 'FixPack upload location')
  }
}