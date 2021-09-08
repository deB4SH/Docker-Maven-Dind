pipeline{
    //global agent - its just an maven jar
    agent {
        kubernetes {
            yaml '''
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: maven
                image: ghcr.io/deb4sh/docker-maven-dind:3.8.2-jdk-11-17.12.0
                command:
                - sleep
                args:
                - 99999
                volumeMounts:
                - name: dockersock
                  mountPath: /var/run/docker.sock
              volumes:
              - name: dockersock
                hostPath:
                  path: /var/run/docker.sock
            '''
            defaultContainer 'maven'
        }
    }
    //options
    options{
            disableConcurrentBuilds()
            buildDiscarder(logRotator(numToKeepStr: '5'))
            disableResume()
            timeout(time: 2, unit: 'HOURS')
    }
    //define trigger
    triggers{
        pollSCM 'H * * * *'
    }
    //stages to build and deploy
    stages {
        stage("Build Images in parallel") {
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-push-token', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    sh 'docker login ghcr.io -u $user -p $pass'
                    parallel(
                       a: {
                            sh 'mvn clean install -f pom.xml'
                            sh 'mvn docker:push -f pom.xml'
                       },
                       b: {
                            sh 'mvn clean install -f pom.xml -Dmavenversion=3.8.2-adoptopenjdk-8'
                            sh 'mvn docker:push -f pom.xml'
                       },
                       c: {
                            sh 'mvn clean install -f pom.xml -Dmavenversion=3.8.2-adoptopenjdk-16'
                            sh 'mvn docker:push -f pom.xml'
                       }
                    )
                }
            }
        }
    }
}
