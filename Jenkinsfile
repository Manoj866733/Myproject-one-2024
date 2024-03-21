pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    
    environment {
        SCANNER_HOME = tool 'sonar'
    }

    stages {
        stage('Git checkout') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/Manoj866733/Myproject-one-2024.git']])
            }
        }
    
        stage('compile') {
            steps {
                sh 'mvn clean compile'
            }
        }
    
        stage('Unittest') {
            steps {
                sh 'mvn test'
            }
        }
        
        stage('SonarQube analysis') {
            steps {
                script {
                    withSonarQubeEnv('sonar-server') {
                        sh ''' 
                            $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=myproject \
                            -Dsonar.java.binaries=. \
                            -Dsonar.projectKey=myproject 
                        '''
                    }
                }
            }
        }
    
        stage('Package with Maven') {
            steps {
                sh 'mvn clean install'
            }
        }
        
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh "docker build -t myproject ."
                        sh "docker tag myproject 866733/projectone:v1 "
                        sh "docker push 866733/projectone:v1 "
                    }
                }
            }
        }
        
        stage('Copy WAR to Tomcat') {
            steps {
                script {
                    sh "cp  /var/lib/jenkins/workspace/myporject/target/petclinic.war /opt/apache-tomcat-9.0.80/webapps/ "
                }
            }
        }
    }
}
