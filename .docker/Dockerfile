FROM  tomcat:9.0-jdk11-openjdk AS BUILDER

# Installation de git
RUN apt-get update && \
    apt-get install -y git-core && \
    rm -rf /var/lib/apt/lists/*

# Installation de axelor et de docgen
RUN git clone https://github.com/Timmy44119/avr-docgen.git /axelor-source

# Lancement du build du WAR
RUN cd /axelor-source && \
    ./gradlew -x test build

FROM  tomcat:9.0-jdk11-openjdk

# Scripts d'init, config et WAR Applicatif
COPY --from=BUILDER /axelor-source/build/libs/axelor-erp-*.war /usr/local/tomcat/webapps/ROOT.war
COPY scripts/docker-entrypoint.sh /
COPY scripts/docker-entrypoint.d/* /docker-entrypoint.d/
COPY conf/axelor-config.properties /usr/local/tomcat/axelor-config.properties

# Installation PSQL Client
RUN apt-get update && \
    apt-get install -y postgresql-client && \
    rm -rf /var/lib/apt/lists/*

#ENTRYPOINT ["/docker-entrypoint.sh"]