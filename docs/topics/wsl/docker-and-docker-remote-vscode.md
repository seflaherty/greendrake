---
tags: wsl, docker
---

## On applications...
What are the challenges that Docker can address? What is the raison d^etre here?
* Management of hosting environments
* Continuity in software delivery
* Efficient use of hardware
* Application portability


## On containers...
A container is a loosely isolated environment for the build, deploy, and run of software packages. Since the code and dependencies are there, the software package will run reliably on any computing environment (from RPi to Cloud, thanks to containerization 😎).


## What isn't Docker?
It's not a Hypervisor (the Docker engine "borrows" from the host OS kernel), but software containerization *is an OS virtualization method*. The Docker engine consists of several components configured as a client-server implementation where the client communicates over a REST API.

* Process: `docker` is a CLI application and is the client portion of Docker.
* Process: `dockerd` is the Docker daemon and is the server portion of Docker. It responds to the client and may interact with other daemons. It has responsibility for the lifecycle of the containers.
* `Docker objects` are other artifacts created and configured to support your Docker deployment. It can include networks, volume storage, plugins, and service objects.


## What is the Stackable Unification File System (Unionfs)?
`Unionfs` are how Docker images are constructed. Like an 🧅, your container builds from branches (including SHA1 IDs!) that incorporate the dependencies for your container. These may be added or subtracted, but in the end it appears as if merged when the container is run. The running container is a non-persistent final writeable layer for the running life of the container.

The 'Dockerfile' is a text artifact that contains the instructions to build and run a Docker image. It begins with a Base Image, and then builds up those onion-y layers to be your software package. 


## Repeated from elsewhere
1. Installation of Docker Desktop with "Use the WSL 2 based engine" enabled at **Settings > General**.
2. Enable the Docker integration for your WSL 2 distributions (aliased as "your Linux" from here on) at **Settings > Resources > WSL Integration**.
3. Confirm that the integrations "took" by opening your Linux and entering `docker --version`. Note: I needed to restart Win10 as I had just installed Docker Desktop.


## Develop in remote containers using VS Code
Recommendation to install a suite of extensions to help the development work:
1. Install the VS Code Remote-WSL extension
2. Install the VS Code Remote-Containers extension
3. Install the VS Code Docker extension


## Get a sample project to containerize
Per the tutorial, we are going to talk about launching a Django web app as the end-result Docker container. The code will be pulled from GitHub and dropped into the active WSL 2 distribution as this yields the best file access performance.
1. From the CLI of your Linux, `cd` into a wsl$-mount folder and run `git clone https://github.com/mattwojo/helloworld-django.git`.
2. Launch VS Code via `code .`
   * The VS Code instance will show and indicator in the lower left corner that you have connected to your Linux.
3. From the VS Code command pallette (`Ctrl + Shift + P`), enter: **Remote-Containers: Open Folder in Container...** and then select the top-level directory of the cloned repo.
4. VS Code will build an image and create artifacts `.devcontainer/`,`.devcontainer/devcontainer.json` and `.devcontainer/Dockerfile`. 
    * The VS Code integrated terminal (`Ctrl + SHift + ~`) is connected to your Linux. Enter `uname -a` or `python --version` to see what its getting from the container definition.
5. Run the container and the development server will start at [localhost](http://127.0.0.1:8000). 


You have successfully configured a remote development environment using Docker Desktop (Windows) powered by your Linux as backend. Coding, build, and deploy all from VS Code 😎.


## Resources
[Microsoft Learn: Intro to Docker Containers](https://docs.microsoft.com/en-us/learn/modules/intor-to-docker-containers)
[Microsoft docs: Get started with Docker remote containers on WSL 2](https://docs.microsoft.com/en-us/windows/wsl/tutorials/wsl-containers)