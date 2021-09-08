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
        pollSCM 'H/30 * * * *'
    }
    //stages to build and deploy
    stages {
        stage ('check: prepare') {
            steps {
                sh '''
                    mvn -version
                    export MAVEN_OPTS="-Xmx1024m"
                '''
            }
        }
        parallel(
            stage('build-3.8.2-jdk-11-17.12.0') {
                when {
                    branch 'master'
                }
                steps {
                    withCredentials([usernamePassword(credentialsId: 'docker-push-token', passwordVariable: 'pass', usernameVariable: 'user')]) {
                        sh 'mvn clean install -f pom.xml'
                        sh 'docker login ghcr.io -u $user -p $pass'
                        sh 'mvn docker:push -f pom.xml'
                    }
                }
            }
            stage('build-3.8.2-jdk-8-17.12.0') {
                when {
                    branch 'master'
                }
                steps {
                    withCredentials([usernamePassword(credentialsId: 'docker-push-token', passwordVariable: 'pass', usernameVariable: 'user')]) {
                        sh 'mvn clean install -f pom.xml -Dmavenversion=3.8.2-adoptopenjdk-8'
                        sh 'docker login ghcr.io -u $user -p $pass'
                        sh 'mvn docker:push -f pom.xml'
                    }
                }
            }
            stage('build-3.8.2-jdk-16-17.12.0') {
                when {
                    branch 'master'
                }
                steps {
                    withCredentials([usernamePassword(credentialsId: 'docker-push-token', passwordVariable: 'pass', usernameVariable: 'user')]) {
                        sh 'mvn clean install -f pom.xml -Dmavenversion=3.8.2-adoptopenjdk-16'
                        sh 'docker login ghcr.io -u $user -p $pass'
                        sh 'mvn docker:push -f pom.xml'
                    }
                }
            }
        )
    }

}
