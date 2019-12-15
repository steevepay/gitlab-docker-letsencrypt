# Gitlab CE + Docker Compose running with auto generate/renew Let's Encrypt Certificate

With this repo you will be able to set up self hosted Gitlab CE as a container over SSL auto generated and auto renewed by a web proxy.

## PREREQUISITES

In order to use this compose file (docker-compose.yml) you must have:

- docker https://docs.docker.com/engine/installation/
- docker-compose https://docs.docker.com/compose/install/
- docker-compose-letsencrypt-nginx-proxy-companion https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion

## HOW TO USE 

1. Close this repository

```bash
$ git clone https://github.com/steevepay/gitlab-docker-letsencrypt.git
```

2. Make a copy of the `.env.example` and rename it to `.env`:

Update this file with your preferences.

```dotenv
#
# Container name for your Portainer
#
CONTAINER_NAME=my-gitlab-container

#
# Path where your Gitlab files will be located
#
GITLAB_DATA_PATH=/data/gitlab/

#
# Your domain (or domains)
#
DOMAINS=gitlab.domain.com,www.gitlab.domain.com

#
# Main domain for SSL certificate
#
MAIN_DOMAIN=gitlab.domain.com

#
# Your email for Let's Encrypt register
#
LETSENCRYPT_EMAIL=your_email@domain.com

#
# Network name
# 
# Your container app must use a network conencted to your webproxy 
# https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion
#
NETWORK=webproxy
```

3. Start the container

```bash
$ docker-compose up -d
```

#### Please keep in mind that when starting for the first time it may take a few moments (even a couple minutes) to get your Let's Encrypt certificates generated.