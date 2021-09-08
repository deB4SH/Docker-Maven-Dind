FROM maven:${mavenversion}
LABEL maintainer=deB4SH(https://github.com/deB4SH)
#download docker and unzip
RUN curl -fsSL https://download.docker.com/linux/static/stable/x86_64/docker-${dockerversion}-ce.tgz -o /tmp/docker.tgz && \
    tar --strip-components=1 -xvzf /tmp/docker.tgz -C /usr/local/bin && \
    rm /tmp/docker.tgz
#change workdir to /srv
WORKDIR /srv
#add default shell call
CMD /bin/bash
