pipeline {
    agent any
    tools{
        jdk 'jdk17'
    }
    environment{
        SCANNER_HOME= tool 'sonar-scanner'
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/dheeraj7444/Python-Webapp.git'
            }
        }
        stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs .'
            }
        }
        stage('OWASP Depentency Check') {
            steps {
               dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DC'
               dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        stage('SONARQUBE ANALYSIS') {
            steps {
                withSonarQubeEnv('sonar') {
               sh " $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=P-webapp -Dsonar.projectKey=P-webapp "
                }
            }      
        }
        stage('Dcoker Build and Tag') {
            steps {
                withDockerRegistry(credentialsId: 'docker-cred', url: 'https://index.docker.io/v1/') {
                   sh'make image'
               }
            }
        }
        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image dheeraj7444/python-webapp:latest '
            }
        }
        stage('Dcoker Push') {
            steps {
                withDockerRegistry(credentialsId: 'docker-cred', url: 'https://index.docker.io/v1/') {
                   sh'make push'
               }
            }
        }
    }
}
