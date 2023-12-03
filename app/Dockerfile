# Choose and name our temporary image.
## Build Stage ##
FROM node:lts-alpine as build-stage

# Take an SSH key as a build argument.
ARG SSH_KEY

# Install dependencies required to git clone.
RUN apk update && \
    apk add --update git && \
    apk add --update openssh

# 1. Create the SSH directory.
RUN mkdir -p /root/.ssh/
# 2. Populate the private key file.
RUN echo "$SSH_KEY" > /root/.ssh/id_rsa
# 3. Set the required permissions.
RUN chmod -R 600 /root/.ssh/
# 4. Add github to our list of known hosts for ssh.
RUN ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts

WORKDIR /app
# Clone a repository
RUN git clone --branch <yourBranch> <your ssh-clone-link>

WORKDIR /app/<repository-name>
# install dependencies
RUN npm install
# build app for production with minification
RUN npm run build

FROM nginx:stable-alpine as production-stage
COPY /app/nginx.conf /etc/nginx/nginx.conf
COPY --from=build-stage /app/<repository-name>/dist /usr/share/nginx/html
EXPOSE 8080
CMD ["nginx", "-g", "daemon off;"]

