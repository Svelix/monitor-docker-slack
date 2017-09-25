<a href="https://github.com/DennyZhang?tab=followers"><img align="right" width="200" height="183" src="https://www.dennyzhang.com/wp-content/uploads/denny/watermark/github.png" /></a>

[![Build Status](https://travis-ci.org/DennyZhang/monitor-docker-slack.svg?branch=master)](https://travis-ci.org/DennyZhang/monitor-docker-slack) [![Docker](https://www.dennyzhang.com/wp-content/uploads/sns/docker.png)](https://hub.docker.com/r/denny/monitor-docker-slack/)

[![LinkedIn](https://www.dennyzhang.com/wp-content/uploads/sns/linkedin.png)](https://www.linkedin.com/in/dennyzhang001) [![Github](https://www.dennyzhang.com/wp-content/uploads/sns/github.png)](https://github.com/DennyZhang) [![Twitter](https://www.dennyzhang.com/wp-content/uploads/sns/twitter.png)](https://twitter.com/dennyzhang001) [![Slack](https://www.dennyzhang.com/wp-content/uploads/sns/slack.png)](https://www.dennyzhang.com/slack)

- File me [tickets](https://github.com/DennyZhang/monitor-docker-slack/issues) or star [the repo](https://github.com/DennyZhang/monitor-docker-slack)

# Introduction
Get Slack Notifications, When Containers Run Into Issues

Read more: https://www.dennyzhang.com/docker_monitor

# General Idea
1. Start a container in the target docker host.
2. This container will query status for all containers.

```curl -XGET --unix-socket /var/run/docker.sock http://localhost/containers/json```

3. Send slack notifications, we get matched of "unhealthy"

# How To Use: Plain Container
- Specify slack credentials via env

```
export SLACK_CHANNEL="#XXX"
export SLACK_USERNAME="XXX"
export SLACK_TOKEN="xoxp-XXX-XXX-XXX-XXXXXXXX"
export MSG_PREFIX="Monitoring On XX.XX.XX.XX"
```

- Start container to check
```
container_name="monitor-docker-slack"
# Stop and delete existing container
docker stop $container_name; docker rm "$container_name"

# Start container to monitor docker healthcheck status
docker run -v /var/run/docker.sock:/var/run/docker.sock \
   -t -d -h $container_name --name $container_name \
   -e SLACK_CHANNEL="$SLACK_CHANNEL" -e SLACK_USERNAME="$SLACK_USERNAME" \
   -e SLACK_TOKEN="$SLACK_TOKEN" -e MSG_PREFIX="$MSG_PREFIX" \
   -e WHITE_LIST="$WHITE_LIST" --restart=always \
   denny/monitor-docker-slack:latest

# Check status
docker logs "$container_name"
```

# How To Use: Docker-compose
```
version: '2'
services:
  monitor-docker-slack:
    container_name: monitor-docker-slack
    image: denny/monitor-docker-slack:latest
    volumes:
     - /var/run/docker.sock:/var/run/docker.sock
    environment:
      SLACK_CHANNEL: "#XXX"
      SLACK_USERNAME: "XXX"
      SLACK_TOKEN: "xoxp-XXX-XXX-XXX-XXXXXXXX"
      MSG_PREFIX: "Monitoring On XX.XX.XX.XX"
    restart: always
```

# More customization
- Add message prefix for the slack notification
```
export MSG_PREFIX="Docker Env in Denny's env"
```
![](images/slack_prefix.png)

- Skip checking certain containers by customizing WHITE_LIST env.
```
export MSG_PREFIX="Docker Env in Denny's env"
export WHITE_LIST="nodeexporter,ngin.*"
```
![](images/slack_whitelist.png)

Code is licensed under [MIT License](https://www.dennyzhang.com/wp-content/mit_license.txt).
