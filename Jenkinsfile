pipeline {
    agent any

    environment {
        GIT_URL = 'https://github.com/Sammyoj10/JavaVulnerableLab.git'
        GIT_BRANCH = 'master'
        MAVEN_HOME = 'C:\\Users\\Sammy\\Downloads\\apache-maven-3.9.8'
        SONARQUBE_URL = 'http://localhost:9000'
        NEXUS_URL = 'http://localhost:8081/repository/maven-snapshots/'
        NEXUS_REPO_ID = 'nexus'
        NEXUS_CREDENTIALS_ID = 'sammy'
        TOMCAT_WEB = "C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\webapps"
        TOMCAT_BIN = "C:\\Program Files\\Apache Software Foundation\\Tomcat 10.1\\bin"
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

        stage('SonarQube Analysis') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'sonar', usernameVariable: 'SONARQUBE_USERNAME', passwordVariable: 'SONARQUBE_PASSWORD')]) {
                    bat "\"${env.MAVEN_HOME}\\bin\\mvn\" sonar:sonar -Dsonar.login=%SONARQUBE_USERNAME% -Dsonar.password=%SONARQUBE_PASSWORD% -Dsonar.host.url=${env.SONARQUBE_URL}"
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
                // Deploying the WAR file to Tomcat
                bat "copy target\\JavaVulnerableLab.war \"${env.TOMCAT_WEB}\\JavaVulnerableLab.war\""
            }
        }

    post {
        always {
            echo 'Cleaning up...'
            cleanWs()
        }
    }
}
