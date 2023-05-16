pipeline {
  agent {
    label 'my-slave'
  }

  tools {
  maven 'Maven3'
  }
  stages {
    stage ('Build') {
      steps {
      sh 'mvn clean install -f pom.xml'
      }
    }
    stage ('Code Quality') {
      steps {
        withSonarQubeEnv('sonarqube') {
        sh 'mvn -f pom.xml sonar:sonar'
        }
      }
    }

    stage ('JaCoCo') {
      steps {
        jacoco()
      }
    }

    stage ('Nexus Upload') {
      steps {
      nexusArtifactUploader(
      nexusVersion: 'nexus3',
      protocol: 'http',
      nexusUrl: '13.42.158.154:8081',
      groupId: 'myGroupId',
      version: '1.0-SNAPSHOT',
      repository: 'maven-snapshots',
      credentialsId: 'e234132e-7b5d-4af7-a92b-740f9ae76e5b',
      artifacts: [
      [artifactId: 'MyWebApp',
      classifier: '',
      file: 'target/MyWebApp.war',
      type: 'war']
      ])
      }
    }

    stage ('DEV Deploy') {
          steps {
          echo "deploying to DEV Env "
          deploy adapters: [tomcat9(credentialsId: '579fbd2e-fd7b-467b-9290-613b00c83c5a', path: '', url: 'http://3.10.239.96:8080')], contextPath: null, war: '**/*.war'
          }
        }
        stage ('Slack Notification') {
          steps {
            echo "deployed to DEV Env successfully"
            slackSend(channel:'mysecondpipelinejob', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
          }
        }
        stage ('DEV Approve') {
          steps {
          echo "Taking approval from DEV Manager for QA Deployment"
            timeout(time: 7, unit: 'DAYS') {
            input message: 'Do you want to deploy?', submitter: 'admin'
            }
          }
        }
         stage ('QA Deploy') {
          steps {
            echo "deploying to QA Env "
            deploy adapters: [tomcat9(credentialsId: '579fbd2e-fd7b-467b-9290-613b00c83c5a', path: '', url: 'http://3.10.239.96:8080')], contextPath: null, war: '**/*.war'
            }
        }
        stage ('QA Approve') {
          steps {
            echo "Taking approval from QA manager"
            timeout(time: 7, unit: 'DAYS') {
            input message: 'Do you want to proceed to PROD?', submitter: 'admin,manager_userid'
            }
          }
        }
        stage ('Slack Notification for QA Deploy') {
          steps {
            echo "deployed to QA Env successfully"
            slackSend(channel:'your slack channel_name', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
          }
        }
  }
}
