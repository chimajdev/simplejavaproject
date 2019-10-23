  node {

  def mvnHome = tool 'Maven3'
  stage('Checkout') {
      checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '97d85bee-a066-4992-85df-749a26f3e19d', url: 'https://github.com/chimajdev/simplejavaproject']]])
  }

stage ('build') {
  sh "${mvnHome}/bin/mvn clean install -f MyWebApp/pom.xml"
}

stage ('Code Quality scan') {
      withSonarQubeEnv('SonarQube') {
      sh "${mvnHome}/bin/mvn -f MyWebApp/pom.xml sonar:sonar"
        }
  }
    
stages {
        stage('Download') {
            steps {
                sh 'echo "artifact file" > generatedFile.txt'
            }
        }
    }
    post {
        always {
            archiveArtifacts artifacts: 'generatedFile.txt', onlyIfSuccessful: true
        }
    }

    stage ('Code coverage') {
      jacoco()
  }

    stage ('DEV Deploy')
    {
      echo "deploying to DEV Env "
      sh 'sudo cp /var/lib/jenkins/workspace/$JOB_NAME/MyWebApp/target/MyWebApp.war /var/lib/tomcat8/webapps'
    }

    stage ('DEV Approve')
      {
            echo "Taking approval from DEV Manager"

            timeout(time: 7, unit: 'DAYS') {
            input message: 'Do you want to deploy?', submitter: 'admin'
            }
    }
  stage ('Slack notification') 

  {
    slackSend(channel:'nevea', message: "Job is successful, here is the info -  Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")

  }

}
