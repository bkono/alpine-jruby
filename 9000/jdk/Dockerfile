FROM gliderlabs/alpine:3.2

ENV JRUBY_VERSION=9.0.1.0

RUN apk-install bash \
  && apk --update add --virtual build-deps wget ca-certificates \
  && mkdir -p /opt/jruby \
  && cd /tmp \
  && wget "https://circle-artifacts.com/gh/andyshinn/alpine-pkg-glibc/6/artifacts/0/home/ubuntu/alpine-pkg-glibc/packages/x86_64/glibc-2.21-r2.apk" \
           "https://circle-artifacts.com/gh/andyshinn/alpine-pkg-glibc/6/artifacts/0/home/ubuntu/alpine-pkg-glibc/packages/x86_64/glibc-bin-2.21-r2.apk" \
  && apk add --allow-untrusted glibc-2.21-r2.apk glibc-bin-2.21-r2.apk \
  && /usr/glibc/usr/bin/ldconfig /lib /usr/glibc/usr/lib \
  && echo 'hosts: files mdns4_minimal [NOTFOUND=return] dns mdns4' >> /etc/nsswitch.conf \
  && wget -O /tmp/jdk.tar.gz http://www.java.net/download/jdk8u60/archive/b18/binaries/jre-8u60-ea-bin-b18-linux-x64-02_jun_2015.tar.gz \
  && cd /opt \
  && tar xzf /tmp/jdk.tar.gz \
  && ln -s /opt/*1.8* /opt/jdk \
  && ln -s /opt/jdk/bin/java /usr/bin/java \
  && rm -rf /opt/jdk/demo /opt/jdk/sample /opt/jdk/man /opt/jdk/src.zip \
  && wget https://s3.amazonaws.com/jruby.org/downloads/${JRUBY_VERSION}/jruby-bin-${JRUBY_VERSION}.tar.gz -O /tmp/jruby.tar.gz \
  && tar -zxf /tmp/jruby.tar.gz -C /opt/jruby \
  && rm /tmp/*.tar.gz \
  && ln -s /opt/jruby/jruby-${JRUBY_VERSION}/bin /usr/local/bin/ruby \
  && apk del build-deps \
  && echo 'gem: --no-rdoc --no-ri' >> ~/.gemrc

ENV PATH /opt/jruby/jruby-${JRUBY_VERSION}/bin:$PATH

RUN echo 'gem: --no-rdoc --no-ri' >> ~/.gemrc

ENV GEM_HOME /usr/local/bundle
ENV PATH $GEM_HOME/bin:$PATH
RUN gem install bundler \
  && bundle config --global path "$GEM_HOME" \
  && bundle config --global bin "$GEM_HOME/bin"

# don't create ".bundle" in all our apps
ENV BUNDLE_APP_CONFIG $GEM_HOME

CMD [ "irb" ]
