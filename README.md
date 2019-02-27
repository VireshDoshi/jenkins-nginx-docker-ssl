# Dockerised Jenkins and Nginx using docker in docker via self signed certificate

### What is this about?
This repository will install a Dockerised jenkins and nginx instance. It will also configure SSL access to your jenkins instance by using a self signed certificate and provide docker usage within Jenkins ( Docker in Docker)

Additional features :
1. Jenkins will use `jenkins` user
2. docker commands via jenkins scripts must be preceded with `sudo`
3. nginx configuration files can be modifed without a full restart
4. jenkins home is persisted
5. nginx log files are viewable from the host machine
6. designed to run on localhost (your local machine only)

### Assumptions for running this repo
You will need to understand bash and docker and jenkins. A basic understanding of networking is useful.

### What is docker in docker
Very simply, docker in docker allows Docker commands to be used inside an existing docker container by utilising the host docker daemon.


### Pre-setup
On your host machine, you will need Docker and Docker Compose installed and verified to be working. These instructions have been tested on a MAC only:
1. Docker Version: 18.09.1 - Community Edition



### How to run this on a non localhost server?
instructions go here

### Steps
1. Clone this repository
2. run the command 'docker-compose build'
3. create the directories `mkdir -p jenkins_home/ logs/nginx/ certs/`
4. create a self signed SSL certificate
  ```
  cd certs
  openssl req -x509 \
    -newkey rsa:4096 \
    -keyout self_signed_key.pem \
    -out self_signed_cert.pem \
    -days 365 \
    -nodes -subj '/CN='localhost
  ```
5. startup `docker-compose up -d`
6.  open up a browser and visit  [https:\\localhost\jenkins](https:\\localhost\jenkins)




### How to configure the browser to accept the self signed certificate
instructions go here

### What is the jenkins UID/GID ?
instructions go here

### What is the docker-entrypoint.sh file?
instructions go here



### Validate Docker from Jenkins

Issue a docker command from the Jenkins container to the host. For example, running `docker ps` from inside of the Jenkins container should result in something like:

```console
$ docker exec -u jenkins jenkins sudo docker ps
CONTAINER ID        IMAGE                      COMMAND                  CREATED             STATUS              PORTS                                                                                NAMES
771a9c9fde17        jenkins.nginx.docker:lts   "/sbin/tini -- /dock…"   11 minutes ago      Up 11 minutes       0.0.0.0:50000->50000/tcp, 8080/tcp, 0.0.0.0:50022->50022/tcp, 0.0.0.0:2022->22/tcp   jenkins
d68e3b96071e        nginx:latest               "nginx -g 'daemon of…"   11 minutes ago      Up 11 minutes       0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp                                             nginx
```


### Useful Nginx Commands

- Validate the nginx configuration:

  ```console
  $ docker exec nginx /usr/sbin/nginx -t
  nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
  nginx: configuration file /etc/nginx/nginx.conf test is successful
  ```

- Repload the nginx file without docker-compose restart:

  ```console
  $ docker exec nginx /usr/sbin/nginx -s reload
  Reloading nginx: nginx.
  ```

### Validate the setup:
1. goto the HTTP url [http://localhost/jenkins](http://localhost/jenkins/) and notice you are redirected to HTTPS
2. create a jenkins job and issue the command `sudo docker ps` and view the host docker running containers


### Deep dive nginx configuration file
- instructions go here


### Deep dive the docker-compose.yml file

- instructions go here

### Azure Active Directory Integration
Follow these instructions for setting up Azure Active Directory Authentication with Jenkins. You will need an Azure subscription to achieve this. For POC purposes, use a trial account.

1. Install `Azure AD` Jenkins Plugin
2. Create a new Application Registration
3. add in a redirect url `<<HTTPS_URL>>/securityRealm/finishLogin`
4. Note down the `application id` and the `tenant id ( directory id)`
5. Create a new client Application secret
6. Change the `Windows Azure Active Directory` API permissions and add the following
    * Application Permissions: `Read directory data`
    * Delegated Permissions: `Sign in and read user profiles`
    * Ensure to click on `grant permissions`
7. Enabled `ID tokens` tick box
    * In `Authentication` page, select `ID tokens` under `Implicit grant`.


### References

[1] inspired by : [https://github.com/mjstealey/jenkins-nginx-docker](https://github.com/mjstealey/jenkins-nginx-docker)
