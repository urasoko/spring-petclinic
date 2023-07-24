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
                // sh "./mvnw compile -Dcheckstyle.skip"
                // sh "./mvnw test -Dcheckstyle.skip"
                sh "./mvnw package -Dcheckstyle.skip -Dmaven.test.skip"
            }
        }

        stage ('Build docker image') {
            steps {
                script {
                    docker.build('soleng.jfrog.io/sokop-docker-local/jen-jf-plg:latest', '.')
                }
            }
        }

        stage('Push') {
            steps {
                jf 'docker push soleng.jfrog.io/sokop-docker-local/jen-jf-plg:latest --build-name=jen-jf-plg --build-number=1.0.0'
            }

        }

        stage('Publish') {
            steps {
                jf 'rt bce jen-jf-plg 1.0.0'
                jf 'rt bag jen-jf-plg 1.0.0'
                jf 'rt bp jen-jf-plg 1.0.0'
            }
        }

        stage('Scan') {
            steps {
                jf 'rt bs jen-jf-plg 1.0.0'
            }
        }
    }
}