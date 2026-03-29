# Docker and NGINX Guide

## 1. Install a webserver like NGINX manually

### On Linux (Ubuntu/Debian)
```bash
sudo apt update
sudo apt install nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```

Then open a browser at:
```text
http://localhost
```

### On CentOS/RHEL
```bash
sudo yum install epel-release
sudo yum install nginx
sudo systemctl start nginx
sudo systemctl enable nginx
```

### On Windows
- Use Docker Desktop and run an `nginx` container, or
- Install WSL2 + Ubuntu, then use the Linux commands above.

> Manual install means installing the webserver package on the OS. Docker is a separate way: you run NGINX inside a container instead of installing it directly on the machine.

---

## 2. Docker basics

### Key concepts
- `Image`: a packaged app and its dependencies. Example: `nginx` image.
- `Container`: a running instance of an image.
- `Registry`: a repository of images.
- `Public registry`: a shared place anyone can pull images from. Example: Docker Hub.

### Public registry
- Docker Hub is the most common public registry.
- It holds images like `nginx`, `ubuntu`, `jenkins`.
- `docker pull nginx` downloads from Docker Hub by default.

---

## 3. What each command does

### `docker pull nginx`
- Downloads the `nginx` image from Docker Hub.
- It does not start anything.
- After this, the image is stored locally.

### `docker run nginx`
- Creates and starts a container from the `nginx` image.
- Runs in the foreground, showing logs in your terminal.
- If you stop the terminal (Ctrl+C), the container stops.

### `docker images`
- Lists all images stored locally.
- Shows image name, tag, image ID, size.

### `docker ps`
- Lists running containers only.

### `docker ps -a`
- Lists all containers, including stopped ones.

### `docker run -p 8088:80 nginx`
- Starts nginx in a container.
- Maps host port `8088` to container port `80`.
- Access webserver at `http://localhost:8088`.

### `docker run -d -p 8073:80 nginx`
- Starts nginx in detached mode (`-d`), so it runs in the background.
- Host port `8073` forwards to container port `80`.

### `docker run -d -p 8082:8080 jenkins`
- Starts Jenkins in the background.
- Maps host port `8082` to Jenkins container port `8080`.

### `docker stop CID`
- Stops a container by its ID or name.
- `CID` means container ID.

### `docker rm CID`
- Removes a stopped container.
- You must stop it first before removing.

### `docker rmi IMAGENAME/nginx`
- Deletes an image from local storage.
- If containers still use it, you may need to remove them first.

---

## 4. Stop/remove all containers using the nginx image

### Stop all nginx-based containers
```bash
docker ps --filter ancestor=nginx -q | xargs -r docker stop
```

### Remove all nginx-based containers
```bash
docker ps --filter ancestor=nginx -q | xargs -r docker rm
```

- `docker ps --filter ancestor=nginx -q`: list all container IDs created from the `nginx` image.
- `xargs -r docker stop`: stop each container.
- Then remove them with `docker rm`.

---

## 5. Docker workflow diagram

```mermaid
flowchart LR
  A[Docker CLI] --> B[Docker Engine]
  B --> C[Local Image Cache]
  B --> D[Running Container]
  C --> D
  B --> E[Public Registry (Docker Hub)]
  A -->|docker pull nginx| E
  E -->|download image| C
  A -->|docker run| D
  D -->|port mapping| F[Host Port 8088/8073]
```

---

## 6. Example simple flow

1. `docker pull nginx`
   - Get image from public registry.
2. `docker run -d -p 8073:80 nginx`
   - Create container and start it in background.
3. `docker ps`
   - See running container.
4. `docker stop <CID>`
   - Stop the container.
5. `docker rm <CID>`
   - Remove the stopped container.

---

## 7. Quick tips

- Use `docker ps` to see current running containers.
- Use `docker ps -a` to see stopped containers too.
- Use `docker logs <CID>` to view container output.
- Use `docker rm -f <CID>` to force remove a running container.
