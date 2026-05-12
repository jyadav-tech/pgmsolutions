What is a Docker and why is it used?
Docker is a software platform that uses containers to package and run applications. It allows you to build, test, and deploy applications quickly. Think of a container as a lightweight, portable, and self-sufficient package that includes everything an application needs to run: the code, a runtime, libraries, and system tools.

This approach ensures that an application will run consistently, regardless of the environment it's running in. It solves the classic "it works on my machine" problem by making sure the application's environment is the same everywhere.

Key Concepts:
Images: A Docker image is a read-only template that contains the instructions for creating a Docker container. It's like a blueprint for an application. You can create your own images or use pre-built ones from a public registry like Docker Hub.
Docker image is a template, but it's not a simple text file. An image is a read-only template that contains the instructions AND the actual application's code, a runtime, libraries, and any other dependencies. It's a complete, portable "snapshot" of an application.

Containers: A Docker container is a runnable instance of an image. It's the actual running application with all its dependencies isolated from the host machine and other containers.

Docker Engine: The Docker Engine is the core technology that builds and runs containers. It's a client-server application with three main components: the server (the Docker daemon), a REST API, and a command-line interface (CLI) client.

The Docker CLI communicates with the Docker daemon using a REST API exposed by the daemon itself, typically over a Unix socket (/var/run/docker.sock) on Linux systems.
Step-by-step:
1. Docker CLI (client)
    You type the command in terminal.
2. CLI sends request via API
    The CLI converts your command into an HTTP request (REST API call).
3. Communication channel
    On Linux → via Unix socket:
    /var/run/docker.sock
On Windows/macOS → via TCP (or internal VM bridge)

Example internally:
POST /containers/create
POST /containers/start

4. Docker Daemon (dockerd)
The daemon:
    →Receives the API request
    →Pulls image (if needed)
    →Creates container
    →Starts container

Bonus: You Can Actually See This
curl --unix-socket /var/run/docker.sock http://localhost/containers/json
This directly hits the Docker REST API (bypassing CLI)

Important Clarification
    →REST API ≠ separate service
    →It's part of dockerd (i.e. Docker Daemon)
    →CLI is just a friendly wrapper over that API

Final Takeaway:
    Docker CLI → REST API → Docker Daemon → Containers
    (REST API is part of the daemon, not a separate component)

------------------------------------------------------------------------
1. Docker is an API-driven system
    →The CLI is just a tool, not the core system
    →The real control plane is the API

👉 As an architect, this helps you:
    →Integrate Docker into CI/CD pipelines
    →Automate infrastructure
    →Debug issues beyond "just commands"

2. Everything can be automated (without CLI)
For example:
    →CI tools (GitHub Actions, Jenkins)
    →Backend services
    →DevOps scripts
👉 They don't "run docker commands" — they:
    →Call APIs
    →Use SDKs (Go, Python, etc.)
That's a big mental shift from developer → architect.

3. This is the foundation of Kubernetes
Kubernetes doesn't use Docker CLI at all.
Instead:
    →It talks to container runtimes via APIs (Container Runtime Interface)
    →Same concept → different abstraction
👉 So what you just understood is:
    →The conceptual bridge between Docker → Kubernetes

4. Debugging Like a Senior Engineer
When something breaks, juniors think:
    "Docker command failed"
You'll think:
    "Is this a client issue, API issue, daemon issue, or container issue?"
That's a huge difference in problem-solving depth.

How You Should Think Going Forward:
When you use any Docker command, mentally map it:

What you type:	        What actually happens:
docker run	            API call to create + start container
docker ps	            API call to list containers
docker logs	            API call to fetch logs

👉 This builds architect-level intuition
------------------------------------------------------------------------

How Docker Works:
Docker uses a client-server architecture. The Docker client (CLI) communicates with the Docker daemon (server) to manage containers. When you run a command like docker run, the client tells the daemon to create and start a new container from a specified image.

A key technology behind Docker is the containerization offered by the Linux kernel. Docker leverages features like namespaces (to provide process isolation) and cgroups (control groups, to limit resource usage) to create these isolated environments.

My understanding: Assume Docker is an application like Git. It has it's own space to contain images.
docker --version

List docker images:
docker images

Check if services are running : Show the running containers, like listing active processes.	
docker ps	[ps stands for "process status"]

Check Apache (web-server) logs in the container:
docker logs ls7-dashboard-web-server-1

How to go inside a docker container:
docker exec -it <docker container name> bash
docker exec -it ls7-dashboard-web-server-1 bash
Explanation of this command:
This command does not start the service; instead, it: Attaches you to the running container named ls7-dashboard-web-server-1 and opens a bash shell inside it — like entering its terminal.
Breakdown of the command:
docker exec: Run a command inside a running container.
-i: Interactive — keeps the input stream open.
-t: TTY — allocates a pseudo-terminal so you can interact with it like a normal terminal.
ls7-dashboard-web-server-1: The name (or ID) of the running container.
bash: Tells Docker to run the Bash shell, so you can type commands inside the container.

Bash: Bash, short for Bourne Again Shell, is a powerful command language interpreter and scripting language widely used in Unix-like operating systems, including Linux and macOS. It allows users to interact with the system, execute commands, and automate tasks through scripts. Essentially, it's a way to communicate with the computer's core functions using text-based commands.
