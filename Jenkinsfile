pipeline {
  agent any

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
      nexusUrl: 'ec2-3-8-100-5.eu-west-2.compute.amazonaws.com:8081',
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
  }
}
