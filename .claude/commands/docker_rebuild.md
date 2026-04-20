# docker_rebuild

Stop and remove the existing container, rebuild the image, and start a new container.

Run these commands in sequence:

```bash
docker stop network-viewer && docker rm network-viewer; docker build --no-cache -t network-viewer . && docker run -d -p 8000:8000 --name network-viewer network-viewer
```

Steps:
1. `docker stop network-viewer && docker rm network-viewer` — stop and remove the existing container (ignore errors if it doesn't exist)
2. `docker build --no-cache -t network-viewer .` — rebuild the image from scratch
3. `docker run -d -p 8000:8000 --name network-viewer network-viewer` — start the container in the background

After completion, the app is available at http://localhost:8000.
