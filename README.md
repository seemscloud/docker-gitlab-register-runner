```yml
cat > docker-compose.yml << "EndOfMessage"
version: '3.9'

services:
  gitlab-runner-config:
    image: gitlab/gitlab-runner:latest
    container_name: gitlab-runner-config
    command:
      - register
    environment:
      CI_SERVER_URL: "http://gitlab.localdomain"
      RUNNER_TAG_LIST: "docker"
      RUNNER_NAME: "docker-runner-XXXXX"
      REGISTRATION_TOKEN: "XXXXX"
      REGISTER_NON_INTERACTIVE: "true"
      RUNNER_EXECUTOR: "docker"
      EXECUTOR: "docker"
      REGISTER_LOCKED: "false"
      DOCKER_IMAGE: "docker:latest"
      DOCKER_PRIVILEGED: "true"
    volumes:
      - gitlab-runner-config:/etc/gitlab-runner:rw
      - /var/run/docker.sock:/var/run/docker.sock
      - /etc/localtime:/etc/localtime:ro
      - /etc/timezone:/etc/timezone:ro
    networks:
      - gitlab-runner

  gitlab-runner-XXXXX:
    image: gitlab/gitlab-runner:latest
    container_name: gitlab-runner-XXXXX
    restart: always
    volumes:
      - gitlab-runner-config:/etc/gitlab-runner:ro
      - /var/run/docker.sock:/var/run/docker.sock
      - /etc/localtime:/etc/localtime:ro
      - /etc/timezone:/etc/timezone:ro
    depends_on:
      - gitlab-runner-config
    networks:
      - gitlab-runner

networks:
  gitlab-runner:
    driver: bridge
    
volumes:
  gitlab-runner-config:
EndOfMessage
```

```bash
docker-compose up --force-recreate --build --remove-orphans --detach
```
