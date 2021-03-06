servers = [dev :"10.0.0.7", qa :"10.0.0.8"]

pipeline {
    agent any
    environment {
      TOMCAT_HOST = "ec2-user"
      TOMCAT_WEB_LOCATION = "/opt/tomcat8/webapps"
      SOURCE_LOCATION = "target/aquaweb.war"
    }
    parameters {
      choice choices: ['dev', 'qa'], description: 'Choose environment to Deploy', name: 'deployEnv'
      choice choices: ['master'], description: 'Choose branch to Deploy', name: 'deployBranch'
    }
    stages {
        stage('git') {
            steps {
                git credentialsId: 'git', url: 'https://github.com/ISR3942/Jenkins'
            }
        }
        stage('mvn install') {
            steps {
                sh script: 'mvn clean deploy'
            }
        }
        stage('Tomcat Deploy Dev') {
            when{
                expression {params.deployEnv == 'dev'} 
            }
            steps {
                sshagent(['tomcat']) {
                    sh script: "scp -o StrictHostKeyChecking=no ${SOURCE_LOCATION} ${TOMCAT_HOST}@${servers[params.deployEnv]}:${TOMCAT_WEB_LOCATION}"
                }
            }
        }
        stage('Tomcat Deploy QA') {
          
            when{
                expression {params.deployEnv == 'qa'} 
            }
            steps {
                sshagent(['tomcat']) {
                    sh script: "scp -o StrictHostKeyChecking=no ${SOURCE_LOCATION} ${TOMCAT_HOST}@${servers[params.deployEnv]}:${TOMCAT_WEB_LOCATION}"
                }
            }
        }
    }
    post {
      always {
        mail body: "Hello Developer, your job, ${env.JOB_NAME}, said ${currentBuild.result}. Build url, ${env.BUILD_URL} ", 
            subject: "jenkins job status", 
             to: 'indukurisriramaraju7@gmail.com',
             from: 'indukuri.sriram@gmail.com'
      }
    }
}
