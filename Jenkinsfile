pipeline {
    agent any

    environment {
        GIT_URL = 'https://github.com/Sammyoj10/JavaVulnerableLab.git'
        GIT_BRANCH = 'master'
        MAVEN_HOME = 'C:\\Users\\Sammy\\Downloads\\apache-maven-3.9.8'
        SONARCLOUD_URL = 'https://sonarcloud.io'
        NEXUS_URL = 'http://localhost:8081/repository/maven-releases/'
        NEXUS_REPO_ID = 'releases'
        NEXUS_CREDENTIALS_ID = 'sammy'
        TOMCAT_WEBAPPS_DIR = 'C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\webapps'
    }

    tools {
        jdk 'JDK17'  // Use the JDK installation configured in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: "${env.GIT_BRANCH}", url: "${env.GIT_URL}"
            }
        }

        stage('Build') {
            steps {
                bat "\"${env.MAVEN_HOME}\\bin\\mvn\" clean package"
            }
        }

        stage('SonarCloud Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar', variable: 'SONARCLOUD_TOKEN')]) {
                    bat "\"${env.MAVEN_HOME}\\bin\\mvn\" sonar:sonar -Dsonar.login=%SONARCLOUD_TOKEN% -Dsonar.host.url=${env.SONARCLOUD_URL}"
                }
            }
        }

        stage('Deploy to Nexus') {
            steps {
                bat "\"${env.MAVEN_HOME}\\bin\\mvn\" deploy -DaltDeploymentRepository=${env.NEXUS_REPO_ID}::default::${env.NEXUS_URL} -Dnexus.username=${env.NEXUS_CREDENTIALS_ID}"
            }
        }

        stage('Copy WAR to Tomcat') {
            steps {
                script {
                    def warFile = bat(script: "for %i in (target\\*.war) do @echo %i", returnStdout: true).trim()
                    bat "copy ${warFile} \"${env.TOMCAT_WEBAPPS_DIR}\""
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up...'
            cleanWs()
        }
    }
}
