FROM alpine/git as builder
ARG GIT_TAG=desktop-v2023.1.1
ARG REPO_URL=https://github.com/bitwarden/clients.git
ARG SOURCE_DIR=bitwarden

WORKDIR /src
RUN git clone https://github.com/bitwarden/clients.git $SOURCE_DIR
RUN cd $SOURCE_DIR && git checkout $GIT_TAG


FROM node:19.4.0
# Prepare an environment for building
ARG SOURCE_DIR=bitwarden
COPY --from=builder /src/$SOURCE_DIR/ /src/
RUN uname -m
RUN apt-get update \
	&& apt-get upgrade -y \
	&& apt-get install -y pkg-config libxss-dev libsecret-1-dev rpm musl-dev musl-tools
RUN npm install -g node-gyp && node-gyp install $(node -v)
RUN curl https://sh.rustup.rs -sSf | sh -s -- -y
ENV PATH="${PATH}:/root/.cargo/bin"
RUN rustup target add aarch64-unknown-linux-musl
# Install all the deps
WORKDIR /src
RUN npm install
WORKDIR /src/apps/desktop
RUN npm install
WORKDIR /src/apps/desktop/desktop_native
RUN npm ci && npm install || true
# Build
RUN npm run build
# Package 
WORKDIR /src/apps/desktop
RUN npm run build:lin 


