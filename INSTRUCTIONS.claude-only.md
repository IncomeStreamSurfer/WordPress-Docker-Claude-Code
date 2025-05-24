# Standalone Claude Code Environment

This document provides instructions on how to run a standalone Claude Code development environment using Docker, without WordPress or a database. This environment provides VS Code Server with Claude Code pre-installed.

## Prerequisites

*   **Docker Desktop** (or Docker Engine and Docker Compose CLI) installed and running.
*   An **Anthropic API Key**. You will need to set this as an environment variable.

## Setup and Running

1.  **Clone the Repository (if you haven't already):**
    ```bash
    git clone <repository-url> # Replace <repository-url> with the actual URL
    cd <repository-directory> # Replace <repository-directory> with the folder name
    ```

2.  **Set your Anthropic API Key:**
    You need to make your Anthropic API key available as an environment variable named `ANTHROPIC_API_KEY`. How you do this depends on your operating system:

    *   **Linux/macOS (Bash/Zsh):**
        You can set it temporarily for your current terminal session:
        ```bash
        export ANTHROPIC_API_KEY="your_api_key_here"
        ```
        Or add this line to your shell configuration file (e.g., `~/.bashrc`, `~/.zshrc`) for it to persist across sessions. Remember to source the file (e.g., `source ~/.bashrc`) or open a new terminal after editing.

    *   **Windows (PowerShell):**
        ```powershell
        $env:ANTHROPIC_API_KEY="your_api_key_here"
        ```
        To set it permanently, search for "environment variables" in the Windows search bar and add `ANTHROPIC_API_KEY` to your user variables.

    *   **Using a `.env` file (Recommended for local development):**
        Create a file named `.env` in the root of the project directory (the same directory as `docker-compose.claude-only.yml`) with the following content:
        ```
        ANTHROPIC_API_KEY=your_api_key_here
        ```
        The `docker-compose.claude-only.yml` file is already configured to pick up this variable if the `.env` file exists. **Make sure to add `.env` to your `.gitignore` file to avoid committing your API key.**

3.  **Build and Start the Container:**
    Navigate to the root directory of the project (where `docker-compose.claude-only.yml` is located) and run:
    ```bash
    docker-compose -f docker-compose.claude-only.yml up --build -d
    ```
    *   `-f docker-compose.claude-only.yml`: Specifies the custom Docker Compose file.
    *   `up`: Builds (if necessary) and starts the container(s).
    *   `--build`: Forces a rebuild of the image (useful if you've changed the Dockerfile or related scripts).
    *   `-d`: Runs the container in detached mode (in the background).

4.  **Access VS Code Server:**
    Once the container is running, open your web browser and go to:
    [http://localhost:8085](http://localhost:8085)

    You should see the VS Code interface.

5.  **Using Claude Code:**
    *   Open a terminal within VS Code Server (Terminal > New Terminal).
    *   Type `claude` or `claude-wrapper` to start interacting with Claude Code.
    *   The `install-claude-code.sh` script (run during Docker image build) sets up an alias `claude` for `claude-wrapper`.

## Managing the Environment

*   **To stop the environment:**
    ```bash
    docker-compose -f docker-compose.claude-only.yml down
    ```

*   **To view logs:**
    ```bash
    docker-compose -f docker-compose.claude-only.yml logs -f
    ```

*   **To restart the environment:**
    ```bash
    docker-compose -f docker-compose.claude-only.yml restart
    ```

## Troubleshooting

*   **Port 8085 is already in use:** If another service on your machine is using port 8085, you can change the host port mapping in `docker-compose.claude-only.yml`. For example, to use port 8086 on your host, change `ports:` to `"- 8086:8080"`.
*   **API Key Issues:** Ensure `ANTHROPIC_API_KEY` is correctly set and accessible to Docker Compose. Using the `.env` file method is generally reliable.
*   **"File not found" for scripts during build (especially on Windows):** Ensure your Git client is configured to handle line endings correctly (e.g., `git config --global core.autocrlf input` for Linux/macOS, or `true` for Windows if you are careful, though `input` is safer cross-platform). The Dockerfile uses `dos2unix`, which should help. If issues persist, try rebuilding with `docker-compose -f docker-compose.claude-only.yml build --no-cache`.
