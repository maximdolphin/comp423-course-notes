# Setting up a dev container for Go

* Primary author: [Maxim Chadaev](https://github.com/maximdolphin)  
* Reviewer: [Daniel Henderson](https://github.com/HendersonDaniel)

## Expectations for the Go Tutorial
- The dev container should use a base image from Microsoft (Hint: refer back to the MkDocs tutorial).
- Ensure the dev container installs the official Go VSCode Plugin (made by the Go Team at Google).
- Demonstrate the `go version` subcommand to prove a recent version of Go is installed.
- Utilize the `go mod` subcommand.
- Use the `go run` subcommand.
- Use the `go build` subcommand, discuss this in the context of COMP211's `gcc` command, run the built binary directly, and discuss the difference between `run` and `build`.

## Initial Setup
In your project:

1. Create a directory `.devcontainer`.
2. Create a Dockerfile `.devcontainer/Dockerfile`.
3. Create a dev container configuration `.devcontainer/devcontainer.json`.
4. Install the Remote Development extension in VS Code.
5. Make sure you have Docker installed. If you use Windows, make sure your project is on a partition shared with Docker.

## Dockerfile
Since Visual Studio Code 1.38 release in August 2019, there is now support for Alpine-based dev containers, which is great for size as Alpine is about 5MB!

For Go, I decided to use the language server `gopls` only.

I also wanted a shell based on zsh and oh-my-zsh.

Finally, I have mapped my SSH keys so that I can interact with Git from inside a terminal window in Visual Studio Code.

Here comes the Dockerfile:

```dockerfile
ARG GO_VERSION=1.13
ARG ALPINE_VERSION=3.10

FROM golang:${GO_VERSION}-alpine${ALPINE_VERSION}
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=1000

# Setup user
RUN adduser $USERNAME -s /bin/sh -D -u $USER_UID $USER_GID && \
    mkdir -p /etc/sudoers.d && \
    echo $USERNAME ALL=$begin:math:text$root$end:math:text$ NOPASSWD:ALL > /etc/sudoers.d/$USERNAME && \
    chmod 0440 /etc/sudoers.d/$USERNAME

# Install packages and Go language server
RUN apk add -q --update --progress --no-cache git sudo openssh-client zsh
RUN go get -u -v golang.org/x/tools/cmd/gopls 2>&1

# Setup shell
USER $USERNAME
RUN sh -c "$(wget -O- https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" "" --unattended &> /dev/null
ENV ENV="/home/$USERNAME/.ashrc" \
    ZSH=/home/$USERNAME/.oh-my-zsh \
    EDITOR=vi \
    LANG=en_US.UTF-8
RUN printf 'ZSH_THEME="robbyrussell"\nENABLE_CORRECTION="false"\nplugins=(git copyfile extract colorize dotenv encode64 golang)\nsource $ZSH/oh-my-zsh.sh' > "/home/$USERNAME/.zshrc"
RUN echo "exec `which zsh`" > "/home/$USERNAME/.ashrc"
USER root
```

* This is based on Go 1.13 on Alpine 3.10.
* It runs without root using the user vscode.
* The final image size is only 445MB.
* It contains an SSH client to interact with your Git repositories using SSH keys.
* It runs the zsh with oh-my-zsh shell with a few plugins.

### Dev Container configuration

```json
{
    "name": "Your project Dev",
    "dockerFile": "Dockerfile",
    "extensions": [
        "golang.go",
        "davidanson.vscode-markdownlint",
        "shardulm94.trailing-spaces",
        "IBM.output-colorizer"
    ],
    "settings": {
        "go.useLanguageServer": true
    },
    "postCreateCommand": "go mod download",
    "runArgs": [
        "-u",
        "vscode",
        "--cap-add=SYS_PTRACE",
        "--security-opt",
        "seccomp=unconfined",
        "-v", "${env:HOME}/.ssh:/home/vscode/.ssh:ro"
    ]
}
```

This installs a few extensions in your container and bind mounts your .ssh directory for Git purposes.

Side note: VS Code is clever enough to ignore comments in devcontainer.json.

It also runs go mod download after the container is set up to download your Go dependencies when the container is ready.

### Launching it

Launch it by opening the VS Code command palette and selecting ```Remote-Containers: Open folder in container...``` and choose the current folder.

This will build the Docker image, install the VS Code dependencies in there, and bind mount everything for you!

Finally, you have a terminal running a beautiful zsh inside VS Code (open a terminal if you don’t see it).

# References
[Citation 1 -> Medium Article](https://medium.com/@quentin.mcgaw/ultimate-go-dev-container-for-visual-studio-code-448f5e031911)
