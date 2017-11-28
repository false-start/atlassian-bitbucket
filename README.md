# Overview

This Docker container makes it easy to get an instance of Bitbucket up and running.

## Requirements

 * Docker
 * Database
 * Disk-Space

# Quickstart
* docker-compose up

## Reverse Proxy Settings

If bitbucket is run behind a reverse proxy server as [described here](https://confluence.atlassian.com/bitbucketserver/proxying-and-securing-bitbucket-server-776640099.html),
then you need to specify extra options to make bitbucket aware of the setup. Since moving to Sprintboot these can be set simply as environment variables and will be picked up automatically.
```
version: '2'
services:
  bitbucket:
    image: strowi/bitbucket:
    build: .
    # ports:
    #   - 7990:7990
    #   - 7999:7999
    depends_on:
      - mysql
    environment:
      - SERVER_PROXY_NAME=localhost
      - SERVER_PROXY_PORT=443
      - SERVER_REDIRECT_PORT=443
      - SERVER_SCHEME=https
      - SERVER_SECURE=true
      - SERVER_CONTEXT_PATH=/stash
      - ELASTICSEARCH_ENABLED=true
      - JAVA_OPTS=
    volumes:
      - ./data/bitbucket:/var/atlassian/bitbucket
```
##  Container-Ports
* `HTTP-Port (default: 7990)
* SSH-Port (default : 7999)

## Logging
TODO: log everything to stdout
Logs will be written to data/logs/


## Backup + Restore

It is sufficient to backup the database and the directory on the server.  Either shut it down before backing up the directory or remove the following files during restore,
otherwise startup will fail with cryptic messages:

```
 rsync root@HOSTNAME:/data/bitbucket \
        --exclude /caches/ \
        --exclude /data/db.* \
        --exclude /shared/data/db.* \
        --exclude /shared/data/repositories \
        --exclude /search/data/ \
        --exclude /shared/search/data/ \
        --exclude /export/ \
        --exclude /log/ \
        --exclude /plugins/.*/ \
        --exclude /tmp \
        --exclude /.lock \
        --exclude /shared/.lock \
        --progress -a \
        ./data/bitbucket/
```

To restore just pre-fill directory + database and start the container. ( fix the file permissions to container-user daemon (uid: 1) ).

## Elasticsearch

For production-grade it is recommended to provide an external elasticsearch-instance. This will enable the code-search-functionality. For local testing you can start
the included elasticsearch by setting the environment-variable `ELASTICSEARCH_ENABLED` to true.


## Java Options

To set additional java commandline-parameters you can use the `JAVA_OPTS`-variable.
