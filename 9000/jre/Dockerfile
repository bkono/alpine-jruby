FROM alpine:3.2

ENV JAVA_VERSION=8 \
    JAVA_UPDATE=45 \
    JAVA_BUILD=14 \
    JAVA_PACKAGE=server-jre \
    JAVA_HOME=/usr/lib/jvm/default-jvm \
    JRUBY_VERSION=9.0.1.0


# Here we use several hacks collected from https://github.com/gliderlabs/docker-alpine/issues/11:
# 1. install GLibc (which is not the cleanest solution at all)
# 2. hotfix /etc/nsswitch.conf, which is apperently required by glibc and is not used in Alpine Linux

RUN apk add --update bash wget ca-certificates && \
    cd /tmp && \
    wget "https://circle-artifacts.com/gh/andyshinn/alpine-pkg-glibc/6/artifacts/0/home/ubuntu/alpine-pkg-glibc/packages/x86_64/glibc-2.21-r2.apk" \
         "https://circle-artifacts.com/gh/andyshinn/alpine-pkg-glibc/6/artifacts/0/home/ubuntu/alpine-pkg-glibc/packages/x86_64/glibc-bin-2.21-r2.apk" && \
    apk add --allow-untrusted glibc-2.21-r2.apk glibc-bin-2.21-r2.apk && \
    /usr/glibc/usr/bin/ldconfig /lib /usr/glibc/usr/lib && \
    echo 'hosts: files mdns4_minimal [NOTFOUND=return] dns mdns4' >> /etc/nsswitch.conf && \
    wget --header "Cookie: oraclelicense=accept-securebackup-cookie;" \
        "http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION}u${JAVA_UPDATE}-b${JAVA_BUILD}/${JAVA_PACKAGE}-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" && \
    tar xzf "${JAVA_PACKAGE}-${JAVA_VERSION}u${JAVA_UPDATE}-linux-x64.tar.gz" && \
    mkdir -p /usr/lib/jvm && \
    mv "/tmp/jdk1.${JAVA_VERSION}.0_${JAVA_UPDATE}" "/usr/lib/jvm/java-${JAVA_VERSION}-oracle" && \
    ln -s "java-${JAVA_VERSION}-oracle" $JAVA_HOME && \
    wget --header "Cookie: oraclelicense=accept-securebackup-cookie;" \
      "http://download.oracle.com/otn-pub/java/jce/8/jce_policy-8.zip" && \
    unzip jce_policy-8.zip && \
    chown -R uucp:143 UnlimitedJCEPolicyJDK8/ && \
    mv UnlimitedJCEPolicyJDK8/*.jar $JAVA_HOME/jre/lib/security/ && \
    ln -s $JAVA_HOME/bin/java /usr/bin/java && \
    rm -rf $JAVA_HOME/jre/bin/jjs \
           $JAVA_HOME/jre/bin/keytool \
           $JAVA_HOME/jre/bin/orbd \
           $JAVA_HOME/jre/bin/pack200 \
           $JAVA_HOME/jre/bin/policytool \
           $JAVA_HOME/jre/bin/rmid \
           $JAVA_HOME/jre/bin/rmiregistry \
           $JAVA_HOME/jre/bin/servertool \
           $JAVA_HOME/jre/bin/tnameserv \
           $JAVA_HOME/jre/bin/unpack200 \
           $JAVA_HOME/jre/lib/ext/nashorn.jar \
           $JAVA_HOME/jre/lib/jfr.jar \
           $JAVA_HOME/jre/lib/jfr \
           $JAVA_HOME/jre/lib/oblique-fonts && \
  mkdir -p /opt/jruby && \
  wget https://s3.amazonaws.com/jruby.org/downloads/${JRUBY_VERSION}/jruby-bin-${JRUBY_VERSION}.tar.gz -O /tmp/jruby.tar.gz && \
  tar -zxf /tmp/jruby.tar.gz -C /opt/jruby && \
  rm /tmp/*.tar.gz && \
  ln -s /opt/jruby/jruby-${JRUBY_VERSION}/bin/jruby /usr/local/bin/ruby && \
  apk del wget ca-certificates && \
  rm -rf /tmp/* /var/cache/apk/*

ENV LANG C.UTF-8
ENV PATH /opt/jruby/jruby-${JRUBY_VERSION}/bin:$JAVA_HOME/bin:$PATH
RUN echo 'gem: --no-rdoc --no-ri' >> ~/.gemrc

ENV GEM_HOME /usr/local/bundle
ENV PATH $GEM_HOME/bin:$PATH
RUN gem install bundler \
  && bundle config --global path "$GEM_HOME" \
  && bundle config --global bin "$GEM_HOME/bin"

# don't create ".bundle" in all our apps
ENV BUNDLE_APP_CONFIG $GEM_HOME

CMD [ "irb" ]
