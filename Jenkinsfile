pipeline {
    agent any
    stages {
        stage ('Clone') {
            steps {
                git url: 'https://github.com/urasoko/spring-petclinic.git', branch: 'main'
            }
        }

        stage ('Artifactory configuration') {
            steps {
                rtServer (
                    id: 'scpspc',
                    url: 'https://scpspc.jfrog.io',
                    credentialsId: 'scpspc-key'
                )
            }
        }

        stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    tool: 'mvn 3.9.0',
                    useWrapper: true,
                    pom: 'pom.xml',
                    goals: 'compile -Dcheckstyle.skip'
                )
                rtMavenRun (
                    tool: 'mvn 3.9.0',
                    useWrapper: true,
                    pom: 'pom.xml',
                    goals: 'test -Dcheckstyle.skip'
                )
                rtMavenRun (
                    tool: 'mvn 3.9.0',
                    useWrapper: true,
                    pom: 'pom.xml',
                    goals: 'package -Dcheckstyle.skip -Dmaven.test.skip'
                )
            }
        }

        stage ('Build docker image') {
            steps {
                script {
                    docker.build('scpspc.jfrog.io/docker-virtual/pet-jen-art:latest', '.')
                }
            }
        }

        stage ('Push image to Artifactory') {
            steps {
                rtDockerPush(
                    serverId: 'scpspc',
                    image: 'scpspc.jfrog.io/docker-virtual/pet-jen-art:latest',
                    targetRepo: 'docker-virtual'
                )
            }
        }

    }
}