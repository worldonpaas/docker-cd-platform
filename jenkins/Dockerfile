FROM java:8

MAINTAINER nk

ENV ITBOX_DATA /projects/itbox/data
ENV ITBOX_SCRIPTS /projects/itbox/scripts
ENV ITBOX_TOOLS /projects/itbox/tools

ENV CATALINA_HOME $ITBOX_TOOLS/tomcat
ENV PATH $CATALINA_HOME/bin:$PATH
ENV JENKINS_HOME $ITBOX_DATA/jenkins
ENV JENKINS_SLAVE_AGENT_PORT 50000
ENV COPY_REFERENCE_FILE_LOG $JENKINS_HOME/copy_reference_file.log
ENV JENKINS_UC https://updates.jenkins-ci.org

RUN mkdir -p "$ITBOX_DATA"
RUN mkdir -p "$ITBOX_SCRIPTS"
RUN mkdir -p "$ITBOX_TOOLS"

RUN mkdir -p "$CATALINA_HOME"
RUN mkdir -p "$JENKINS_HOME"

#VOLUME /projects/itbox/data
#VOLUME /projects/itbox/tools
#VOLUME /projects/itbox/scripts

# (PART 1/2): Make my tomcat setup ready
COPY tomcat/apache-tomcat-8.0.28.tar.gz ${CATALINA_HOME}/

WORKDIR ${CATALINA_HOME}

RUN gunzip ${CATALINA_HOME}/apache-tomcat-8.0.28.tar.gz
RUN tar -xvf ${CATALINA_HOME}/apache-tomcat-8.0.28.tar

RUN mv apache-tomcat-8.0.28/* ${CATALINA_HOME}/
RUN /bin/rm -rf apache-tomcat-8.0.28

# Clear my tomcat container
RUN /bin/rm -rf ${CATALINA_HOME}/webapps/ROOT/*

# (PART 2/2): Make my jenkins setup ready
# Add my jenkins.war file
ADD bin/jenkins.war ${CATALINA_HOME}/webapps/ROOT/

RUN mkdir -p ${ITBOX_DATA}/jenkins_ref/init.groovy.d
COPY scripts/init.groovy.d/tcp-slave-agent-port.groovy ${ITBOX_DATA}/jenkins_ref/init.groovy.d/

WORKDIR $CATALINA_HOME/webapps/ROOT

RUN jar -xvf jenkins.war

EXPOSE 8080
EXPOSE 50000

#CMD ["catalina.sh", "run"]
ENTRYPOINT ["catalina.sh", "run"]

COPY scripts/plugins.sh ${ITBOX_SCRIPTS}/
