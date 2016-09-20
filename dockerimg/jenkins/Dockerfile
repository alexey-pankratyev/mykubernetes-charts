#FROM jenkins
FROM csanchez/jenkins-slave
ENV RUBY_VERSION 2.2.3
ENV HO /var/jenkins_home

USER root

# change default bash
RUN rm /bin/sh && ln -s /bin/bash /bin/sh

# Install essentials packages
# RUN apt-get update && apt-get install DEBIAN_FRONTEND=noninteractive install -yq build-essential curl gnupg git vim

RUN apt-get update -q \
    && DEBIAN_FRONTEND=noninteractive apt-get install -yq --no-install-recommends \
      build-essential \
      curl \
      libssl-dev \
      gnupg \
      git \
      ruby \
      vim

RUN [ -d $HO ]  && chown -R jenkins $HO || echo "not"

# Install mysql client (no server)
#RUN DEBIAN_FRONTEND=noninteractive apt-get install -yq mysql-client libmysqlclient-dev

# Download RVM as root
RUN curl -#LO https://rvm.io/mpapis.asc && gpg --import mpapis.asc
RUN \curl -sSL https://get.rvm.io | bash -s stable

# Install RVM requirements
RUN /bin/bash -lc "rvm requirements"

# Add jenkins to rvm group
RUN usermod -a -G rvm jenkins

# Install Ruby
RUN /bin/bash -lc  "rvm install $RUBY_VERSION && rvm install jruby && rvm use $RUBY_VERSION --default"

# Set no doc for gem
RUN echo "gem: --no-rdoc --no-ri" >> ~/.gemrc

# Install bundler
RUN /bin/bash -lc  "gem install bundler --no-doc --no-ri"
ADD ./utils/Gemfile ~/Gemfile
RUN  /bin/bash -lc  "bundle install --gemfile=~/Gemfile"

ADD ./utils/bashrc_rvm ~/.bashrc

# Add Jenkins Log folder
RUN mkdir /var/log/jenkins \
    && touch /var/log/jenkins/jenkins.log \
    && chown -R  jenkins:jenkins /var/log/jenkins

# slim down image
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/man/?? /usr/share/man/??_*

# Already exposed 8080 & 50000 ports in Jenkins image
