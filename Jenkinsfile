pipeline {  
agent {
    label 'slave1'
  }
  environment{
     CODEDIR='/var/lib/jenkins/workspace/webapp-build-pipeline/webapp-project-pipeline'
    }
  stages {
     stage('clone repo') {
       steps {
        sh "rm -rf *"
        sh "git clone -b develop git@github.com:giridharpatti/webapp-project-pipeline.git"
       }
     }
    stage('Build') {
      steps {
        dir("${env.CODEDIR}") {
        echo 'Building the code with maven'
        sh '''
        mvn clean package &&
        mv /var/lib/jenkins/workspace/webapp-build-pipeline/webapp-project-pipeline/target/mywebapp.war /var/lib/jenkins/workspace/webapp-build-pipeline/webapp-project-pipeline/target/mywebapp-"${BUILD_NUMBER}".war 
        '''
        }
      }
    }
    stage('Copy code to s3 bucket') {
      steps {
        dir("${env.CODEDIR}") {
        echo 'Building the code with maven'
        sh '''
        aws s3 cp /var/lib/jenkins/workspace/webapp-build-pipeline/webapp-project-pipeline/target/mywebapp-"${BUILD_NUMBER}".war s3://mywebapp-code/mywebapp-"${BUILD_NUMBER}".war
        '''
        }
      }
    }
  }
  post {
        always {
            mail to: 'radhakrishna.devops2022@gmail.com',
                subject: "Jenkins pipeline Build: ${env.JOB_NAME} - ${currentBuild.currentResult}", 
                body: "${currentBuild.currentResult}- \"${env.JOB_NAME}\" build: ${env.BUILD_NUMBER}\n\nView the log at:\n ${env.BUILD_URL}\n\nBlue Ocean:\n${env.RUN_DISPLAY_URL}"
        }
    }
}

