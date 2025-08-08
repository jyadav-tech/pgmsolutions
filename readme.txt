What is a Docker and why is it used?
Docker is a software platform that allows you to build, test, and deploy applications quickly. Docker packages software into standardized units called containers that have everything the software needs to run including libraries, system tools, code, and runtime.

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
