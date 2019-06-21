FROM jenkins/jenkins:lts

USER root
RUN apt-get update && \
  apt-get install -y autoconf bison build-essential libssl-dev libyaml-dev \
  libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm3 libgdbm-dev && \
  rm -rf /var/lib/apt/lists/*

USER jenkins

COPY src /usr/share/jenkins/ref/src

COPY plugins.txt /usr/share/jenkins/ref/plugins.txt
RUN /usr/local/bin/install-plugins.sh < /usr/share/jenkins/ref/plugins.txt

ENV RUBY_BUILD_DEFINITIONS=$JENKINS_HOME/src/ruby-build
#ENV RUBY_BUILD_ROOT=$JENKINS_HOME/src/ruby-build
#ENV RUBY_BUILD_SKIP_MIRROR=true
ENV PATH=$JENKINS_HOME/.rbenv/shims:$JENKINS_HOME/.rbenv/bin:$PATH
