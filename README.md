Docker-Maven-Dind
====

# Why?
This image is created to resolve an issue in my homelab project to build docker images with maven. Why maven?
Please take a look into the pom in this directory. I'm used to use maven. I like maven. It allowes to inject variables into the docker file, through the fabric8 maven plugin.


# Whats available?
All packages are listed under: https://github.com/deB4SH/Docker-Maven-Dind/pkgs/container/docker-maven-dind

* ``3.8.2-adoptopenjdk-16-17.12.0``
    * Maven: 3.8.2-adoptopenjdk-16
    * Docker: 17.12.0
* ``3.8.2-adoptopenjdk-8-17.12.0``
    * Maven: 3.8.2-adoptopenjdk-8
    * Docker: 17.12.0
* ``3.8.2-jdk-11-17.12.0``
    * Maven: 3.8.2-adoptopenjdk-11
    * Docker: 17.12.0
  
# Usage in Jenkinsfile
Using the image inside a Jenkinsfile with a cloud plugin installed is pretty straight forward. Take a close look onto the following snippet.

````yaml
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
````
Also available under: https://github.com/deB4SH/Docker-Maven-Dind/blob/master/Jenkinsfile#L3