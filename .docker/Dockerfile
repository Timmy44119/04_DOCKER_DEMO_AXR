FROM  tomcat:9.0-jdk11-openjdk AS BUILDER

# Installation de git
RUN apt-get update && \
    apt-get install -y git-core && \
    rm -rf /var/lib/apt/lists/*

# Installation de axelor et de docgen
RUN mkdir -p /axelor-source && \
    cd /axelor-source && \
    git clone https://github.com/axelor/open-suite-webapp.git axelor-erp && \
    sed -e 's|git@github.com:|https://github.com/|' -i axelor-erp/.gitmodules && \
    cd axelor-erp && \
    git checkout master && \
    git submodule sync && \
    git submodule init && \
    git submodule update && \
    git submodule foreach git checkout master && \
    git submodule foreach git pull origin master && \
    rm -f /axelor-source/axelor-erp/build.gradle && \
    rm -f /axelor-source/axelor-erp/gradle.properties

COPY conf/build.gradle /axelor-source/axelor-erp/build.gradle
COPY conf/gradle.properties /axelor-source/axelor-erp/gradle.properties

# Lancement du build du WAR
RUN cd /axelor-source/axelor-erp && \
    ./gradlew  clean build -x test

FROM  tomcat:9.0-jdk11-openjdk

# Scripts d'init, config et WAR Applicatif
COPY --from=BUILDER /axelor-source/axelor-erp/build/libs/axelor-erp-*.war /tmp/ROOT.war
COPY scripts/docker-entrypoint.sh /
COPY scripts/docker-entrypoint.d/* /docker-entrypoint.d/
COPY conf/axelor-config.properties /usr/local/tomcat/axelor-config.properties

# Installation PSQL Client
RUN apt-get update && \
    apt-get install -y postgresql-client && \
    rm -rf /var/lib/apt/lists/*

ENTRYPOINT ["/docker-entrypoint.sh"]