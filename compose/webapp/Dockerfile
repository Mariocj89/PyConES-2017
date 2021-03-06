# Dockerfile for PyConES Web App
FROM ubuntu:16.04
ENV PYTHONUNBUFFERED 1

# Ubuntu packages required
RUN apt-get update && apt-get install -y \
     software-properties-common
RUN add-apt-repository multiverse
RUN apt-get update && apt-get install -y \
    build-essential \
    git \
    libpq-dev \
    python3 \
    python3-dev \
    python3-pip \
    nginx \
    libjpeg-dev \
    jpegoptim \
    optipng \
    gettext \
    curl

# Install nodejs, bower and less
RUN set -ex \
  && for key in \
    9554F04D7259F04124DE6B476D5A82AC7E37093B \
    94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
    0034A06D9D9B0064CE8ADF6BF1747F4AD2306D93 \
    FD3A5288F042B6850C66B31F09FE44734EB7990E \
    71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
    DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
    B9AE9905FFD7803F25714661B63B535A4C206CA9 \
    C4F0DFFF4E8C1A8236409D08E73BC641CC11F4C8 \
  ; do \
    gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
  done
ENV NPM_CONFIG_LOGLEVEL info
ENV NODE_VERSION 6.9.4
RUN curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.xz" \
  && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
  && gpg --batch --decrypt --output SHASUMS256.txt SHASUMS256.txt.asc \
  && grep " node-v$NODE_VERSION-linux-x64.tar.xz\$" SHASUMS256.txt | sha256sum -c - \
  && tar -xJf "node-v$NODE_VERSION-linux-x64.tar.xz" -C /usr/local --strip-components=1 \
  && rm "node-v$NODE_VERSION-linux-x64.tar.xz" SHASUMS256.txt.asc SHASUMS256.txt


# Requirements and webapp user and group
COPY ./requirements /requirements
RUN pip3 install -U pip
RUN pip3 install -r /requirements/production.txt \
    && groupadd -r webapp \
    && useradd -r -g webapp webapp

# Source code
COPY . /app
RUN chown -R webapp /app

# Creates entrypoint and start scripts
COPY ./compose/webapp/gunicorn.sh /gunicorn.sh
COPY ./compose/webapp/entrypoint.sh /entrypoint.sh
RUN sed -i 's/\r//' /entrypoint.sh \
    && sed -i 's/\r//' /gunicorn.sh \
    && chmod +x /entrypoint.sh \
    && chown webapp /entrypoint.sh \
    && chmod +x /gunicorn.sh \
    && chown webapp /gunicorn.sh

WORKDIR /app

ENTRYPOINT ["/entrypoint.sh"]
