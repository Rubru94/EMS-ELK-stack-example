# globaldevtools.bbva.com:5000/jenkinsagents/je-mm-cib/tibco/bw5.14
FROM centos:7

RUN yum update -y && \
	yum install -y 	git \
					bc \
					wget \
					unzip \
					curl \
					lib32z1 \
					lib32ncurses5 \
					lib32stdc++6 \
					lib32z1-dev \
					xorg-x11-server-Xvfb.x86_64 \
					libXrender \
					libXtst \
					glibc.i686

RUN useradd -ms /bin/bash --uid 1000 --home /home/jenkins jenkins \
  && git config --system user.name "jenkins" \
  && git config --system user.email "graas.piaas.support.group@bbva.com" \
  && mkdir /home/jenkins/.ssh \
  && mkdir /opt/tibco \
  && mkdir /opt/tibco/scripts \
  && mkdir /tmp/install \
  && chown -Rf jenkins: /opt/tibco \
  && chown -Rf jenkins: /tmp/install \
  && chown -Rf jenkins: /home/jenkins/.ssh

ARG USER_ARTIFACTORY
ARG KEY_ARTIFACTORY
ARG IMAGE_NAME
ARG URI_REGISTRY
ARG IMAGE_TAG

ARG TIBCO_EMS="TIB_ems_8.6.0_linux_x86_64.zip"

#TIBCO EMS install
COPY ./$TIBCO_EMS /tmp/install/$TIBCO_EMS
RUN unzip /tmp/install/$TIBCO_EMS -d /tmp/install/tibems/ && \
    cd /tmp/install/tibems/TIB_ems_8.6.0 && \
	yum install -y rpm/*.rpm && \
	rm -rf /tmp/install/tibems/ /tmp/install/$TIBCO_EMS && \
	mkdir -p /opt/tibco/config/tibco/cfgmgmt/ems/data/datastore && \
	cp -p /opt/tibco/ems/8.6/samples/config/*.conf /opt/tibco/config/tibco/cfgmgmt/ems/data
	
RUN chown -Rf jenkins: /opt/tibco

USER jenkins

ENV JAVA_HOME=$JHOME
ENV DISPLAY=localhost:1.0
ENV PATH=$PATH:/opt/tibco/ems/8.6/bin
ENV TZ="Europe/Madrid"
RUN date

#Create Start script
RUN touch /opt/tibco/scripts/Start.sh
RUN chmod 777 "/opt/tibco/scripts/Start.sh"
RUN echo "#!/bin/bash" >> /opt/tibco/scripts/Start.sh
RUN echo "cd /opt/tibco/config/tibco/cfgmgmt/ems/data" >> /opt/tibco/scripts/Start.sh
RUN echo "/opt/tibco/ems/8.6/bin/tibemsd64 -config /opt/tibco/config/tibco/cfgmgmt/ems/data/tibemsd.conf" >> /opt/tibco/scripts/Start.sh


CMD /opt/tibco/scripts/Start.sh

# timezone env with default
ENV TZ Europe/Madrid	

EXPOSE 7222
