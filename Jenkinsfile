pipeline {
    agent any
    
    tools {
        jdk 'jdk11'
        maven 'maven3'
    }
    
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout ') {
            steps {
                git branch: 'main', changelog: false, poll: false, url: 'https://github.com/Reliable-Royalty-29/SpringBoot-WebApplication.git'
            }
        }
        
        stage('Code Compile') {
            steps {
                    sh "mvn compile"
            }
        }
        
        stage('Run Test Cases') {
            steps {
                    sh "mvn test"
            }
        }
        
        stage('Sonarqube Analysis') {
            steps {
                    withSonarQubeEnv('sonar-server') {
                        sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Java-WebApp \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=Java-WebApp '''
    
                }
            }
        }
        
        stage('OWASP Dependency Check') {
            steps {
                   dependencyCheck additionalArguments: '--scan ./   ', odcInstallation: 'DP'
                   dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Maven Build') {
            steps {
                    sh "mvn clean package"
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'D', toolName: 'docker') {
                            sh "docker build -t webapp ."
                            sh "docker tag webapp dheeman29/webapp:latest"
                            sh "docker push dheeman29/webapp:latest "
 
                   }
                }   
            }
        }
        
        stage('Docker Image scan') {
            steps {
                    sh "trivy image dheeman29/webapp:latest "
            }
        }
        
         stage('Deploy Container using Docker Image') {
            steps {
                sh 'docker run -d --name springboot -p 8087:8087 dheeman29/webapp:latest'
            }
        }
        
    }
}
