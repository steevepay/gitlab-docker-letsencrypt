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
VIRTUAL_HOST=gitlab.domain.com,www.gitlab.domain.com


#
# Your domain (or domains) for SSL certificate
#
LETSENCRYPT_HOST=gitlab.domain.com,www.gitlab.domain.com

#
# Your email for Let's Encrypt register
#
LETSENCRYPT_EMAIL=your_email@domain.com

#
# Main domain for SSL certificate and gitlab
#
MAIN_DOMAIN=gitlab.domain.com

#
# Network name
# 
# Your container app must use a network conencted to your webproxy 
# https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion
#
NETWORK=webproxy
```
3. Validate and view the docker-compose configuration before starting.

```bash
$ docker-compose config
```

4. Start the container.

During the build time, the environment variables are injected into the image.

```bash
$ docker-compose up -d
```

**Please keep in mind that when starting for the first time it may take a few moments (even a couple minutes) to get your Let's Encrypt certificates generated**

## Pre-configure Gitlab CE

 You can pre-configure the GitLab Docker image by adding the environment variable `GITLAB_OMNIBUS_CONFIG` to the docker-compose file. This variable can contain any gitlab.rb setting and will be evaluated before loading the container’s gitlab.rb file. That way you can easily configure GitLab’s external URL, make any database configuration or any other option from the Omnibus GitLab template.

 Here is an example of pre-configuring OVH STMP server for the gitlab:

```yaml
version: "3.7"

services:
  gitlab-letsencrypt:
    image: 'gitlab/gitlab-ce:latest'
    container_name: ${CONTAINER_NAME}
    restart: always
    hostname: ${MAIN_DOMAIN}
    volumes:
      - '${GITLAB_DATA_PATH}config:/etc/gitlab'
      - '${GITLAB_DATA_PATH}logs:/var/log/gitlab'
      - '${GITLAB_DATA_PATH}data:/var/opt/gitlab'
    restart: unless-stopped
    env_file:
      - ./.env
    environment:
      GITLAB_OMNIBUS_CONFIG: |
        gitlab_rails['smtp_enable'] = true
        gitlab_rails['smtp_address'] = 'ssl0.ovh.net'
        gitlab_rails['smtp_port'] = 465
        gitlab_rails['smtp_user_name'] = 'hello@mail.com'
        gitlab_rails['smtp_password'] = ''
        gitlab_rails['smtp_domain'] = 'ssl0.ovh.net'
        gitlab_rails['smtp_authentication'] = 'login'
        gitlab_rails['smtp_enable_starttls_auto'] = true
        gitlab_rails['smtp_tls'] = true
        gitlab_rails['smtp_openssl_verify_mode'] = 'none'
    networks:
      - default

networks:
  default:
    external:
      name: ${NETWORK}
```

For more options about configuring GitLab please check the [Omnibus GitLab documentation](https://docs.gitlab.com/omnibus/settings/configuration.html).
