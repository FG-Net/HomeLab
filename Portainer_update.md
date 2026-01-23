# Update Portainer (on Docker)

Documentation: https://docs.portainer.io/start/upgrade/docker

## Portainer Server

- #### Stop and remove the old version:

  ```bash
  docker stop portainer
  ```

  ```bash
  docker rm portainer
  ```

- #### (Optional) Update the system:

  ```bash
  apt update && apt upgrade -y && apt autoremove -y && apt autoclean -y
  ```

- #### Pull and start the new version:

  ```bash
  docker pull portainer/portainer-ce:lts
  ```

  ```bash
  docker run -d -p 8000:8000 -p 9443:9443 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:lts
  ```

## Portainer Agent

- #### Stop and remove the old version:

  ```bash
  docker stop portainer_agent
  ```

  ```bash
  docker rm portainer_agent
  ```

- #### (Optional) Update the system:

  ```bash
  apt update && apt upgrade -y && apt autoremove -y && apt autoclean -y
  ```

- #### Pull and start the new version:

  ```bash
  docker pull portainer/agent:lts
  ```

  ```bash
  docker run -d -p 9001:9001 --name portainer_agent --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/volumes:/var/lib/docker/volumes portainer/agent:lts
  ```
