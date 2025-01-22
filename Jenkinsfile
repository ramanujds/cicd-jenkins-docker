pipeline {
    agent any

    tools {
        maven "maven-3.9.9"
    }

    stages {
        stage('Source') {
            steps {
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github-credentials', url: 'https://github.com/ramanujds/cicd-jenkins-docker.git']])
                }
        }

        stage('test') {
            steps {
                sh "mvn test"
                echo 'Test Success'
            }
        }

        stage('Build') {
            steps {
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ram1uj/spring-boot-app ."
                echo 'Docker Image Build Success'
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernameColonPassword(credentialsId: 'docker-login', variable: 'docker_login')])  {
                        sh 'docker login -u ram1uj -p ${docker_login}'
                        sh "docker push ram1uj/spring-boot-app"
                        echo 'Docker Image Push Success'
                    }
                }
            }
        }
    }

    post {
        success {
            junit '**/target/surefire-reports/TEST-*.xml'
            archiveArtifacts 'target/*.jar'
            echo 'Build Success'
        }
    }
}