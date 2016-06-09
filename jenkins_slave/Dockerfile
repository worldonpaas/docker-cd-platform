FROM java:8

MAINTAINER nk

ENV user jenkins
ENV USER_HOME /homme/${user}

RUN yum -y update
RUN yum -y install openssh-server
RUN yum -y install openssh-clients
RUN yum -y install java-1.7.0-openjdk

RUN echo "root:password" | chpasswd

RUN ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key -N ''
RUN ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key -N ''

RUN mkdir -p /var/run/sshd

RUN sed -i 's|session    required     pam_loginuid.so|session    optional     pam_loginuid.so|g' /etc/pam.d/sshd

RUN mkdir -p /var/jenkins_home /bin \
 && useradd -d /var/jenkins_home/worker -u 1000 -m -s /bin/sh jenkins
 
#COPY swarm_jars/swarm-client.jar /bin/

RUN curl -k -L http://repo.jenkins-ci.org/releases/org/jenkins-ci/plugins/swarm-client/2.1/swarm-client-2.1-jar-with-dependencies.jar -o /bin/swarm-client.jar

COPY docker-entrypoint.sh /
RUN chmod a+x /docker-entrypoint.sh
 
WORKDIR /var/jenkins_home/worker

USER jenkins

ENTRYPOINT ["/docker-entrypoint.sh"]

CMD ["java", "-Xmx2048m", "-jar", "/bin/swarm-client.jar", "-fsroot", "/var/jenkins_home/worker"]
