import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=1840a927-5a97-4624-a17e-2e1d4149900d',
        'AZURE_TENANT_ID=0adb040b-ca22-4ca6-9447-ab7b049a22ff']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'QuickstartJenkins-rg'
      def webAppName = 'newjenkinss-app-pratik100'
      // login Azure
      withCredentials([usernamePassword(credentialsId: 'pratikgite100', passwordVariable: 'Pratik1@#', usernameVariable: 'azureServicePrinciple')]) {
       sh '''
          az login --service-principal -u $pratikgite100 -p $Pratik1@# -t $0adb040b-ca22-4ca6-9447-ab7b049a22ff
          az account set -s $1840a927-5a97-4624-a17e-2e1d4149900d
        '''
      }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
}
