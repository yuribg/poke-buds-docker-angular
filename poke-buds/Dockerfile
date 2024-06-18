# syntax=docker/dockerfile:1.4

# Stage 1: Compile and Build angular codebase

# Use official node image as the base image
# FROM --platform=$BUILDPLATFORM node:current-bullseye-slim as builder
FROM node:20-alpine as builder

# Set the working directory
RUN mkdir /project
WORKDIR /project

# Install all the dependencies
RUN npm install -g @angular/cli@latest
# RUN apk update && apk add npm
# RUN npm install --only=production -g @angular/cli@latest && npm cache clean --force && npm install -g typescript

COPY **/package.json **/package-lock.json ./
RUN npm ci

# Generate the build of the application
RUN npm run build:prod

# Add the source code to app
COPY . .

ENV NODE_ENV=production

FROM builder as dev-envs

RUN <<EOF
apt-get update
apt-get install -y --no-install-recommends git
EOF

RUN <<EOF
useradd -s /bin/bash -m vscode
groupadd docker
usermod -aG docker vscode
EOF

# install Docker tools (cli, buildx, compose)
COPY --from=gloursdocker/docker / /

CMD ["ng", "serve", "--host", "0.0.0.0"]


# Stage 2: Serve app with nginx server

# Use official nginx image as the base image
FROM nginx:latest

# Copy the build output to replace the default nginx contents.
COPY --from=builder ./dist /usr/share/nginx/html

# Expose port 80
EXPOSE 80