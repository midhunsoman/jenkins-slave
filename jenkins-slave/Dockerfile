FROM registry.access.redhat.com/rhel7/rhel

ENV JENKINS_SWARM_VERSION 2.0
ENV JNLP_SLAVE_VERSION 2.52
ENV HOME /opt/jenkins-slave
ENV MAVEN_VERSION 3.8.4
ENV JAVA_HOME /usr/lib/jvm/java


RUN sed -i 's/enabled=1/enabled=0/g' /etc/yum/pluginconf.d/subscription-manager.conf 
RUN cat /etc/yum/pluginconf.d/subscription-manager.conf 

RUN cd /usr/local/ && \
    curl -O https://builds.openlogic.com/downloadJDK/openlogic-openjdk/8u262-b10/openlogic-openjdk-8u262-b10-linux-x64.tar.gz && \
    tar -xvzf openlogic-openjdk-8u262-b10-linux-x64.tar.gz && \
    ls openlogic-openjdk-8u262-b10-linux-64

ENV PATH=$PATH:/usr/local/openlogic-openjdk-8u262-b10-linux-64/bin
RUN java -version


RUN useradd -u 1001 -r -m -d ${HOME} -s /sbin/nologin -c "Jenkins Slave" jenkins-slave && \
    mkdir -p /opt/jenkins-slave/bin /var/lib/jenkins && \
    curl -O  http://archive.apache.org/dist/maven/maven-3/3.8.4/binaries/apache-maven-3.8.4-bin.tar.gz && \
    tar -zvxf apache-maven-3.8.4-bin.tar.gz -C /usr/share \
      && mv /usr/share/apache-maven-3.8.4 /usr/share/maven \
      && ln -s /usr/share/maven/bin/mvn /usr/bin/mvn

# Copy script
COPY jenkins-slave.sh /opt/jenkins-slave/bin/

# Download plugin and modify permissions
RUN curl --create-dirs -sSLo /opt/jenkins-slave/bin/swarm-client-$JENKINS_SWARM_VERSION-jar-with-dependencies.jar http://repo.jenkins-ci.org/releases/org/jenkins-ci/plugins/swarm-client/$JENKINS_SWARM_VERSION/swarm-client-$JENKINS_SWARM_VERSION-jar-with-dependencies.jar \
  && curl --create-dirs -sSLo /opt/jenkins-slave/bin/slave.jar http://repo.jenkins-ci.org/public/org/jenkins-ci/main/remoting/$JNLP_SLAVE_VERSION/remoting-$JNLP_SLAVE_VERSION.jar \
  && chmod -R 775 /opt/jenkins-slave /var/lib/jenkins && \
  chown -R jenkins-slave:root /opt/jenkins-slave /var/lib/jenkins

WORKDIR /var/lib/jenkins

VOLUME /var/lib/jenkins

USER 1001

ENTRYPOINT ["/opt/jenkins-slave/bin/jenkins-slave.sh"]
