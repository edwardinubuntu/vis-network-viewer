# vis-network-viewer

## At the Start of Each Conversation

Proactively inform the user of the available custom slash commands at the beginning of each conversation:

---

**Available Custom Slash Commands:**

| Command            | Description                                                                                                                                      |
|--------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| `/docker_rebuild`  | Stop and remove the existing container, rebuild the image, and start a new container. The service will be available at <http://localhost:8000>   |

---

## Project Description

This is a network graph visualization tool that uses the vis-network library to render node-and-edge relationship diagrams.

- Service port: 8000
- Docker image name: `network-viewer`
- Container name: `network-viewer`

Place the JSON files you want to analyze in the `./files` directory.
