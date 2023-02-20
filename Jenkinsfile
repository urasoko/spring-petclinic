pipeline {
    agent none
    stages {
        stage('Compile, Test, Package') {
            agent { docker { image 'eclipse-temurin:17-jdk-alpine' } }
            steps {
                git url: 'https://github.com/urasoko/spring-petclinic.git', branch: 'main'
                sh "./mvnw compile -Dcheckstyle.skip"
                sh "./mvnw test -Dcheckstyle.skip"
                sh "./mvnw package -Dcheckstyle.skip -Dmaven.test.skip"
            }
        }
        stage('Image, push') {
            agent any
            environment {
                JFROG_CREDS = credentials('jfrog-art-creds')
            }            
            steps {
                sh "docker login -u $JFROG_CREDS_USR -p $JFROG_CREDS_PSW scpspc.jfrog.io"
                sh "docker build --tag scpspc.jfrog.io/docker-virtual/petclinic ."
                sh "docker push scpspc.jfrog.io/docker-virtual/petclinic"
            }
        }
    }
}
