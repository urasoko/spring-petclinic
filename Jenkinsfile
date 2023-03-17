pipeline {
    agent any
    tools {
        jfrog 'jfrog-cli'
    }
    stages {
        stage('Clone') {
            steps {
                git branch: 'jen-jf-plg', url: "https://github.com/urasoko/spring-petclinic"
            }
        }

        stage('Exec Maven commands') {
            steps {
                sh "./mvnw compile -Dcheckstyle.skip"
                sh "./mvnw test -Dcheckstyle.skip"
                sh "./mvnw package -Dcheckstyle.skip -Dmaven.test.skip"
            }
        }

        stage ('Build docker image') {
            steps {
                script {
                    docker.build('sokop-nginx:8082/docker-local/jen-jf-plg:latest', '.')
                }
            }
        }

        stage('Push') {
            steps {
                jf 'docker push sokop-nginx:8082/docker-local/jen-jf-plg:latest'
            }
        }
    }
}