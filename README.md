# How I Built an AI Chatbot on Raspberry Pi
Project Summary:
After two weeks of hard work, I successfully built an AI chatbot on my Raspberry Pi using Ollama and Docker with a WebUI interface. Below are the detailed steps I followed so that other students can replicate the process.
## Step-by-step Installation Guide

### 1. System Update
First, I updated the system to make sure everything is up to date:
```
sudo apt update
```
```
sudo apt upgrade
```

### 2. Install Ollama
Go to https://ollama.com and copy the install script:
```
curl -fsSL https://ollama.com/install.sh | sh
```
After installation, check if Ollama works:
```
ollama --version
```
Then pull a model. I used gemma:2b:
```
ollama pull gemma:2b
```
Test the model:
```
ollama run gemma:2b
```
### 3. Install Docker

Make sure required dependencies are installed:
```
sudo apt-get install -y ca-certificates curl gnupg
```
Create a keyring directory:
```
sudo install -m 0755 -d /etc/apt/keyrings
```
Add Docker’s GPG key:
```
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
```
```
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
Add Docker’s repository:
```
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] \
https://download.docker.com/linux/debian \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
### Update packages again:
```
sudo apt-get update
```
Install Docker:
```
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```
Verify Docker is working:
```
sudo docker run hello-world
```
**(Optional)** Add your user to the Docker group:
```
sudo usermod -aG docker $USER
newgrp docker
```

## 4. Run Open WebUI

Start Open WebUI in Docker:
```
docker run -d \
--network=host \
-v open-webui:/app/backend/data \
-e OLLAMA_BASE_URL=http://127.0.0.1:11434 \
--name open-webui \
--restart always \
ghcr.io/open-webui/open-webui:main
```

#### This command does the following:
- --network=host: Uses host network for accessing Ollama.
- -v open-webui:/app/backend/data: Stores data in a Docker volume.
- --name open-webui: Names the container.
- --restart always: Ensures it restarts if it crashes.
- The image ghcr.io/open-webui/open-webui:main is for the web UI.

## How to Use the Chatbot

- Open your browser and go to:
  - ```http://localhost:8080```
  - or from another device:
    - ```http://<your_raspberry_pi_ip>:8080```
- Create an admin account (the first user becomes the admin).
- Start chatting with the model (Gemma, LLaMA, etc.).

## Managing the Container
- Stop:
```
docker stop open-webui
```
- Start:
```
docker start open-webui
```
- Logs:
```
docker logs open-webui
```

## Troubleshooting

Port Conflict: If port 8080 is in use, use another port:
```
-p 8081:8080
```
Permission Issues: Add user to Docker group.
ARM64 Devices (Raspberry Pi 4/5): Use the ARM-compatible image:
```
ghcr.io/open-webui/open-webui:main-arm64
```
## Updating Open WebUI
```
docker stop open-webui
docker rm open-webui
docker pull ghcr.io/open-webui/open-webui:main
docker run -d --network=host -v open-webui:/app/backend/data -e OLLAMA_BASE_URL=http://127.0.0.1:11434 --name open-webui --restart always ghcr.io/open-webui/open-webui:main
```

#### Finally :
```
docker start open-webui
```
- Go to:
```http://localhost:8080```
  - Or from another device:
    ```http://<your_raspberry_pi_ip>:8080```


# The End:

So yeah, this setup creates a clean and user-friendly web UI for the Ollama AI model using Docker. You pull the latest image, connect it to your local Ollama server, and just like that — you’ve got a browser-based interface ready to use.

The container runs in the background, keeps your data safe using a Docker volume, and is configured to restart automatically if your system reboots. It’s efficient, reliable, and easy to manage — making your AI interaction smoother and more accessible.
