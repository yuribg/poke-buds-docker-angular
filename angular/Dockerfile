# syntax=docker/dockerfile:1.4

# Stage 1: Compile and Build angular codebase

# Use official node image as the base image
FROM --platform=$BUILDPLATFORM node:current-bullseye-slim as builder

# Set the working directory
RUN mkdir /project
WORKDIR /project

# Install all the dependencies
RUN npm install -g @angular/cli@latest

COPY **/package.json **/package-lock.json ./
RUN npm ci

# Generate the build of the application
RUN npm run build

# Add the source code to app
COPY . .
CMD ["ng", "serve", "--host", "0.0.0.0"]

# FROM builder as dev-envs
# 
# RUN <<EOF
# apt-get update
# apt-get install -y --no-install-recommends git
# EOF
# 
# RUN <<EOF
# useradd -s /bin/bash -m vscode
# groupadd docker
# usermod -aG docker vscode
# EOF

# install Docker tools (cli, buildx, compose)
COPY --from=gloursdocker/docker / /

CMD ["ng", "serve", "--host", "0.0.0.0"]


# Stage 2: Serve app with nginx server

# Use official nginx image as the base image
FROM nginx:latest

# Copy the build output to replace the default nginx contents.
COPY --from=builder ./dist/angular /usr/share/nginx/html

# Expose port 80
EXPOSE 80