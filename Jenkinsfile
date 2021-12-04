pipeline {
  agent any
 
  tools {
  maven 'Maven3'
  }
  
  stages {
      
     stage ("checkout")  {
         steps {
       checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: '9a71c43b-0c7c-4a81-b171-95e39c4821fa', url: 'https://github.com/etarty2021/myapprepo.git']]])
    }
  }
  
    stage ('Build') {
      steps {
      sh 'mvn clean install -f MyWebApp/pom.xml'
      }
    }
    stage ('Code Quality') {
      steps {
        withSonarQubeEnv('SonarQube') {
        sh 'mvn -f MyWebApp/pom.xml sonar:sonar'
        }
      }
    }
    stage ('JaCoCo') {
      steps {
      jacoco()
      }
    }
    
    stage ('DEV Deploy') {
      steps {
      echo "deploying to DEV Env "
      deploy adapters: [tomcat9(credentialsId: '8a2a21ff-1a24-4707-a1a8-b8853dfa4d5b', path: '', url: 'http://ec2-3-145-95-191.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
      }
    }
    stage ('Slack Notification') {
      steps {
        echo "deployed to DEV Env successfully"
        slackSend(channel:'project3', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
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
        deploy adapters: [tomcat9(credentialsId: '8a2a21ff-1a24-4707-a1a8-b8853dfa4d5b', path: '', url: 'http://ec2-3-145-95-191.us-east-2.compute.amazonaws.com:8080')], contextPath: null, war: '**/*.war'
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
        slackSend(channel:'project3', message: "Job is successful, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
      }
      
    }  
  }
}  
