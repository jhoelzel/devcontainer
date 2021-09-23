# Golang Dev Container
The aim of this project is to generate a permanent development container on my host OS.
I am using windows and i would like to have consistent dev environment that is concurrent with the real production environment.
This too keeps the host machine clean as docker and VS-Code are the only requirement.
In practise this could be used to run your entire dev environment in kubernetes (or any other container management system), though i dont know why you would except maybe you are developing on an Ipad.

## What do u use this for mainly?
- kubernetes operator development
- active kubernetes deployment end to end: The direct integration of kubectl and helm into this project makes it possible to push your entire dev env to a (private!!) registry of your choice and to be used throughout the team.
- with the use of git submodules this project becomes perfect for go: the relative path the container runs in is the go root with my username (changeable in the devcontainer.json)
- development of go-micro based services

## Build arguments
Build arguments can be used to modify the different versions of the tools installed, yet the go tools mostly run @latest

```
    "GO_VERSION": "1.16", // Golang Version
    "ARCH": "amd64", //Your ARCH Note: When your target arch is not amd64 you may need to play a little
    "ALPINE_VERSION": "3.14", // Alpine version
    "KUBECTL_VERSION": "v1.21.4", //kubectl version
    "OPERATOR_SDK_VERSION": "v1.12.0", //Operator SDK version
    "TIMEZONE": "Europe/Berlin", //your Timezone
    "HELM_VERSION": "3.6.3", //Helm version
    "LOCALE": "en_US.UTF-8" //Alpine locale
```

## includes:
- Alpine Golang Dev Container
- ZSH with autocomplete and theme
- sudo openssh-client bash zsh make docker-cli curl git nano gcc libc-dev musl-dev build-base autoconf automake libtool protobuf
- Kubectl
- Helm
- Operator SDK
- Intall go dev Modules /go/bin in module mode for vs code.
- Running User is VSCODE
- Installs Protobuf requirements
- Extra key bindings for keyboard END POS1 and DEL
- Timezone and Language definitions for Alpine

## Docker Socket Passthrough
- uses docker in docker through docker socket passthrough in devcontainer.json mounts
- build images in docker container and use docker as if you where on your regular OS

## VS Code Extentitions
Extentions can be added and removed in the devcontainer.json
- "golang.Go",
- "ms-vscode.go",
- "davidanson.vscode-markdownlint",
- "shardulm94.trailing-spaces",
- "IBM.output-colorizer",
- "ms-kubernetes-tools.vscode-kubernetes-tools", //kubernetes ms tools
- "redhat.vscode-yaml" // Kubernetes, Drone syntax highlighting

## Volume mounts
This project uses volume mounts to mount your git and kubectl config, please adjust these accordingly in the devcontainer.json

# Usage:
Fork and fix:

1) git repo fork git@github.com:JHoelzel/go-devcontainer --clone

2) Change the git paths username to yours in the devcontainer.json in:
- workspaceFolder
- workspaceMount

3) Open VS-Code with Remote tools installed and it will directly ask you to launch the container

Clone and use yourself:

```
git clone https://github.com/jhoelzel/go-devcontainer newproject
cd ./newnewproject
git remote remove origin // remove old origin
git remote add origin https://github.com/user/targetrepo // Add your NEW target repo (create first)
git push --set-upstream origin main // set the upstream target for the main branch and push to new repo instead
# Known Issues:
```

## go dlv-dap for VS code currently does not install automatically
in the dockerfile we find #RUN go get -v dlv-dap@latest commented out because dlv-dap is not @latest compatible... vs code will install it for us once we start