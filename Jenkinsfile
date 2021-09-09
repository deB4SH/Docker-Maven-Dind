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
            when {
                branch 'master'
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-push-token', passwordVariable: 'pass', usernameVariable: 'user')]) {
                    sh 'docker login ghcr.io -u $user -p $pass'
                    sh '''
                        mvn -version
                        export MAVEN_OPTS="-Xmx1024m"
                    '''
                }
            }
        }
        stage('Build images in parallel'){
            when {
                branch 'master'
            }
            parallel {
                stage('build-3.8.2-jdk-11-17.12.0') {
                    steps {
                        sh 'mvn clean install -f pom.xml -Dmavenversion=3.8.2-jdk-11 -Ddockerversion=17.12.0-ce'
                        sh 'mvn docker:push -f pom.xml -Dmavenversion=3.8.2-jdk-11 -Ddockerversion=17.12.0-ce'
                    }
                }
                stage('build-3.8.2-jdk-11-20.10.8') {
                    steps {
                        sh 'mvn clean install -f pom.xml -Dmavenversion=3.8.2-jdk-11 -Ddockerversion=20.10.8'
                        sh 'mvn docker:push -f pom.xml -Dmavenversion=3.8.2-jdk-11 -Ddockerversion=20.10.8'
                    }
                }
                stage('build-3.8.2-jdk-8-17.12.0') {
                    steps {
                        sh 'mvn clean install -f pom.xml -Dmavenversion=3.8.2-adoptopenjdk-8 -Ddockerversion=17.12.0-ce'
                        sh 'mvn docker:push -f pom.xml -Dmavenversion=3.8.2-adoptopenjdk-8 -Ddockerversion=17.12.0-ce'
                    }
                }
                stage('build-3.8.2-jdk-8-20.10.8') {
                    steps {
                        sh 'mvn clean install -f pom.xml -Dmavenversion=3.8.2-adoptopenjdk-8 -Ddockerversion=20.10.8'
                        sh 'mvn docker:push -f pom.xml -Dmavenversion=3.8.2-adoptopenjdk-8 -Ddockerversion=20.10.8'
                    }
                }
                stage('build-3.8.2-jdk-16-17.12.0') {
                    steps {
                        sh 'mvn clean install -f pom.xml -Dmavenversion=3.8.2-adoptopenjdk-16 -Ddockerversion=17.12.0-ce'
                        sh 'mvn docker:push -f pom.xml -Dmavenversion=3.8.2-adoptopenjdk-16 -Ddockerversion=17.12.0-ce'
                    }
                }
                stage('build-3.8.2-jdk-16-20.10.8') {
                    steps {
                        sh 'mvn clean install -f pom.xml -Dmavenversion=3.8.2-adoptopenjdk-16 -Ddockerversion=20.10.8'
                        sh 'mvn docker:push -f pom.xml -Dmavenversion=3.8.2-adoptopenjdk-16 -Ddockerversion=20.10.8'
                    }
                }
            }
        }

    }
}
