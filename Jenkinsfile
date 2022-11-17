def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]
pipeline {
    agent any
    tools{
        maven 'localMaven'
        jdk 'localJdk'
    }
    stages {
        stage('Git checkout') {
            steps {
                echo 'Cloning the application code...'
                git branch: 'master', url: 'https://github.com/dibangoxx/Jenkins-CI-CD-Pipeline-Project.git'
                sh 'mvn --version'
            }
        }
        stage('Build') {
            steps {
                sh 'java -version'
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'archiving....'
                    archiveArtifacts artifacts: '**/*.war', followSymlinks: false
                }
            }
        }
    stage('Unit Test'){
        steps {
            sh 'mvn test'
        }
    }
    stage('Integration Test'){
        steps {
          sh 'mvn verify -DskipUnitTests'
        }
    }
    stage ('Checkstyle Code Analysis'){
        steps {
            sh 'mvn checkstyle:checkstyle'
        }
        post {
            success {
                echo 'Generated Analysis Result'
            }
        }
    }
    stage ('SonarQube scanning'){
        steps {
            withSonarQubeEnv('SonarQube') {
                
            sh """
            mvn sonar:sonar \
          -Dsonar.projectKey=Maven-project \
          -Dsonar.host.url=http://54.152.194.221:9000 \
          -Dsonar.login=8748cb7937f68740ef5180826520ddcb4c49cb1c 
          
            """
            }
        }
    }
    
    
    stage("Upload artifact to Nexus"){
      steps{
       sh 'mvn clean deploy -DskipTests'
        }
    }
    
    stage('Deploy to DEV') {
      environment {
        HOSTS = "dev"
      }
      steps {
        sh "ansible-playbook ${WORKSPACE}/deploy.yaml --extra-vars \"hosts=$HOSTS workspace_path=$WORKSPACE\""
      }
     }
    
    
    
    
    }
}

//slackSend channel: '#mbandi-cloudformation-cicd', message: "Please find the pipeline status of the following ${env.JOB_NAME ${env.BUILD_NUMBER} ${env.BUILD_URL}"
