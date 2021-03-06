FROM ubuntu:18.04

ARG RUNNER_VERSION
ARG DOCKER_VERSION

# fix "debconf: delaying package configuration, since apt-utils is not installed"
RUN apt update \
  && apt install sudo curl ca-certificates -y --no-install-recommends \
  && apt install -y --no-install-recommends apt-utils \
  && curl -L -o docker.tgz https://download.docker.com/linux/static/stable/x86_64/docker-${DOCKER_VERSION}.tgz \
  && tar zxvf docker.tgz \
  && install -o root -g root -m 755 docker/docker /usr/local/bin/docker \
  && rm -rf docker docker.tgz \
  && curl -L -o /usr/local/bin/dumb-init https://github.com/Yelp/dumb-init/releases/download/v1.2.2/dumb-init_1.2.2_amd64 \
  && chmod +x /usr/local/bin/dumb-init \
  && adduser --disabled-password --gecos "" --uid 1000 runner \
  && usermod -aG sudo runner \
  && echo "%sudo   ALL=(ALL:ALL) NOPASSWD:ALL" > /etc/sudoers

RUN mkdir -p /runner \
  && cd /runner \
  && curl -L -o runner.tar.gz https://github.com/actions/runner/releases/download/v${RUNNER_VERSION}/actions-runner-linux-x64-${RUNNER_VERSION}.tar.gz \
  && tar xzf ./runner.tar.gz \
  && rm runner.tar.gz \
  && ./bin/installdependencies.sh

COPY scripts/* /runner/
RUN sh /runner/basic.sh
RUN sh /runner/git.sh
RUN sh /runner/nodejs.sh
RUN sh /runner/ruby.sh
RUN sh /runner/python.sh
RUN sh /runner/java-tools.sh
RUN sh /runner/bazel.sh
RUN sh /runner/aws.sh
RUN sh /runner/android.sh
RUN sh /runner/terraform.sh

# android env variables
ENV ANDROID_HOME "/usr/local/lib/android/sdk"
ENV ANDROID_SDK_ROOT "/usr/local/lib/android/sdk"
ENV M2_REPO "/usr/local/lib/m2"

COPY entrypoint.sh /runner

USER runner:runner
ENTRYPOINT ["/usr/local/bin/dumb-init", "--"]
CMD ["/runner/entrypoint.sh"]
