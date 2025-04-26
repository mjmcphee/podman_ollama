# Running Ollama and OpenWebUI on an Apple Silicon-based Macbook Pro with Podman

These are the steps I used to use OpenWebUI with Ollama using Podman on my MacBook Pro M4.

For Podman specifically, the OpenWebUI documentation recommends using the following command:

```bash
podman run -d --network=slirp4netns:allow_host_loopback=true --name openwebui -p 3000:8080 -v open-webui:/app/backend/data ghcr.io/open-webui/open-webui:main
```

The key difference when using Podman with OpenWebUI is how it connects to your locally installed Ollama instance. Docker is weird, and Podman is a little quirkier. Here's a step-by-step guide:

## Step 1: Ensure Ollama is Installed and Running Locally

First, make sure Ollama is properly installed on your Mac and running. I used Homebrew `brew install ollama`, but you can also visit the [Ollama Website](https://ollama.com/) and download an installer.  To each their own!

You can verify this by looking for the Ollama icon in your Mac's menu bar or by running `ollama list` in your terminal to see installed models.

## Step 2: Run OpenWebUI with Podman

Run the OpenWebUI container with this command:

```bash
podman run -d --network=slirp4netns:allow_host_loopback=true --name openwebui -p 3000:8080 -v open-webui:/app/backend/data ghcr.io/open-webui/open-webui:main
```

## Step 3: Configure the Connection to Ollama

After running the container, you may need to configure OpenWebUI to connect to your local Ollama instance. Mine picked up the previously installed Ollama instance, but in the event it does not you can simply add it here:

1. Open your browser and go to `http://localhost:3000`
2. Navigate to **Settings > Admin Settings > Connections**
3. Create a new Ollama API connection pointing to `http://10.0.2.2:11434` (Podman's special address to reach the host machine)

## Troubleshooting Connectivity Issues

If you're still having trouble connecting OpenWebUI to Ollama, try these steps:

1. **Verify Ollama is running and accessible**: 
   Make sure you can run `ollama list` in your terminal and get a response.

2. **Check for network issues**:
   Sometimes you might see connection errors when trying to access the Ollama API from Podman containers. In these cases, you might need to adjust your network configuration. I had to ensure that Local Network Access was allowed in my macOS **Settings > Privacy & Security > Local Network** menu.

3. **Try alternative network modes**:
   You can experiment with different Podman network settings if the default doesn't work. For example:
   ```bash
   podman run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name openwebui ghcr.io/open-webui/open-webui:main
   ```

4. **Check for outdated volumes**:
   If you've previously used OpenWebUI with Podman, there might be database migration issues. I ran into this with an improperly installed container (poor translation of the Docker commands to my Podman host). Try removing the content of the data directory and restarting the container.

## Alternative Approach: Connect to OpenAI-compatible API

If you continue to have issues with Ollama connectivity, you can try using Ollama's OpenAI-compatible API endpoint instead:

1. In your Ollama settings, ensure the OpenAI compatibility API is enabled
2. In OpenWebUI, add a connection to the OpenAI API instead of direct Ollama connection
3. Point it to `http://10.0.2.2:11434/v1` or your local Ollama OpenAI-compatible endpoint
