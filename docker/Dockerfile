FROM ruby:3.4-bookworm

RUN apt-get update -qq \
 && apt-get install -y --no-install-recommends \
    build-essential \
    default-libmysqlclient-dev \
    git \
    vim \
 && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace
ENV PATH="/workspace/bin:${PATH}"
