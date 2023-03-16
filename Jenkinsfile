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
                jf 'mvn-config --repo-resolve-releases maven-virtual --repo-resolve-snapshots maven-virtual --repo-deploy-releases maven-virtual --repo-deploy-snapshots maven-virtual'
                sh "./mvnw compile -Dcheckstyle.skip"
                sh "./mvnw test -Dcheckstyle.skip"
                sh "./mvnw package -Dcheckstyle.skip -Dmaven.test.skip"
            }
        }

        stage ('Build docker image') {
            steps {
                script {
                    docker.build('sokop-nginx:8082/docker-local/pet-jen-jf:latest', '.')
                }
            }
        }

        stage('Push') {
            environment {
                JFROG_CREDS = credentials('jfrog-art-creds')
            }
            steps {
                sh "docker login -u $JFROG_CREDS_USR -p $JFROG_CREDS_PSW sokop-nginx:8082"
                sh 'docker push sokop-nginx:8082/docker-local/pet-jen-jf:latest'
            }
        }
    }
}