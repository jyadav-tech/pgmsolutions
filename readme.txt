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

======================================================================
DOCKER, KUBERNETES - ALL TERMS UNDERSTANDING WITH DIAGRAMS
======================================================================

  A layman-friendly guide using one simple restaurant analogy, with enough technical detail for a Senior Technical Lead / Solution Architect.

======================================================================
1. THE BIG PICTURE - IMAGINE YOU RUN A RESTAURANT BUSINESS
======================================================================

Let's imagine your company runs an online food-ordering system called LiveSafe Restaurant App.

Your application has PHP code such as:

    index.php
    users.php
    broadcast.php

Think of the application as a restaurant.

We will use the restaurant analogy to understand:

  - GitHub
  - CircleCI
  - Docker
  - Docker Image
  - Docker Container
  - AWS ECR
  - EC2
  - Kubernetes
  - Pod
  - Multiple containers
  - Deployment

The goal is to understand how all these pieces fit together.

======================================================================
2. YOUR PHP CODE = RECIPE
======================================================================

You write PHP code:

    index.php
    users.php
    broadcast.php

Think of this as your recipe.

It tells the restaurant workers:

  "This is how we prepare the food."

In software:

  "This is how the application should work."

Your source code lives in GitHub.

    Developer
        |
        v
     GitHub
        |
        +-- PHP source code
        +-- Configuration
        +-- Tests
        +-- Dockerfile

======================================================================
3. DOCKER IMAGE = READY-MADE FOOD PACKAGE
======================================================================

Imagine you don't want to send your recipe to a customer and ask them to cook it.

Instead, you prepare everything in advance:

    PHP
    Apache
    Required PHP extensions
    Your application code
    Configuration
    Dependencies

Then you put everything into one package.

That package is your Docker image.

For example:

    ls7-dashboard:2.1.3

Think:

  Docker Image = complete packaged application

A Docker image is not normally the running application itself. It is the package/template from which containers are created.

======================================================================
4. DOCKER CONTAINER = RUNNING RESTAURANT
======================================================================

The image is the package.

When you actually run the image:

    Docker Image
         |
         v
    Docker Container

Now the application is running.

So:

    Image       = packaged restaurant
    Container   = restaurant currently open

For example:

    ls7-dashboard:2.1.3
            |
            +----------------+
            |                |
            v                v
      Container 1       Container 2
       (running)         (running)

======================================================================
5. ONE IMAGE CAN CREATE MULTIPLE CONTAINERS
======================================================================

This is a very important Docker concept.

Suppose you have:

    ls7-dashboard:2.1.3

You can create several running containers from that same image:

                  Docker Image
              ls7-dashboard:2.1.3
                        |
           +------------+------------+
           |            |            |
           v            v            v
       Container     Container    Container
           1             2            3

All three containers can be based on exactly the same image version.

This is useful when you need several copies of the same application.

======================================================================
6. AWS ECR = COMPANY WAREHOUSE
======================================================================

Now imagine your company has a secure warehouse where all the ready-made restaurant packages are stored.

That is AWS ECR - Elastic Container Registry.

So:

    Your computer
         |
         v
    Docker Image
         |
         v
    AWS ECR

ECR can contain versions such as:

    ls7-dashboard:2.1.1
    ls7-dashboard:2.1.2
    ls7-dashboard:2.1.3

Think of ECR as:

  Your company's private Docker image warehouse.

This is useful because the deployment system can pull a specific, known version.

For example:

    Production is running:
    ls7-dashboard:2.1.2

If version 2.1.3 has a problem, the team may be able to deploy:

    ls7-dashboard:2.1.2

again.

That gives us a very important architectural idea:

  Versioned application artifacts make deployment and rollback more predictable.

======================================================================
7. CIRCLECI = QUALITY CHECKER
======================================================================

Before putting the food package into the warehouse, someone checks:

  "Is the food good?"

In your company's flow, that role is largely handled by CircleCI.

A simplified flow is:

    Developer
        |
        | git push
        v
     GitHub
        |
        | webhook
        v
     CircleCI
        |
        +--> Checkout code
        |
        +--> Install dependencies
        |
        +--> Run tests
        |
        +--> Build application
        |
        +--> Build Docker image
        |
        +--> Push image to ECR
        |
        v
     AWS ECR

If everything passes, something like this may be stored:

    ls7-dashboard:2.1.3

The exact CircleCI steps depend on your company's configuration.

======================================================================
8. JENKINS = DELIVERY MANAGER
======================================================================

Now the application package is safely sitting in the warehouse.

Someone needs to take that package to the actual restaurant location.

In your office flow, that is where Jenkins comes in.

A simplified view:

                      Jenkins
                         |
                         | "Deploy version 2.1.3"
                         v
                       ECR
                         |
                         | Pull image
                         v
                 Production environment
                         |
                         v
                     Application

Jenkins is essentially saying:

  "Deploy this particular version."

Exactly how Jenkins performs the deployment depends on your infrastructure.

It could deploy to:

  - EC2 + Docker
  - ECS
  - Kubernetes
  - another platform

Do not assume the runtime is Kubernetes until you inspect your Jenkins pipeline or deployment configuration.

======================================================================
9. THE COMPLETE OFFICE FLOW
======================================================================

Your modern software delivery flow can be thought of as:

                     YOU
                      |
                      | git push
                      v
                   GitHub
                      |
                      | webhook
                      v
                  CircleCI
                      |
                 Run tests
                      |
              Build Docker image
                      |
                      v
                   AWS ECR
                      |
              ls7-dashboard:2.1.3
                      |
                      v
                   Jenkins
                      |
                    Deploy
                      |
                      v
              Production environment
                      |
                 Application
                      |
                      v
                   Browser

The major architectural change from traditional FTP deployment is:

Old approach
------------

    Developer
        |
        v
     GitHub
        |
        v
     FTP
        |
        v
    Production server
        |
        +-- Replace PHP files

Modern container approach
-------------------------

    Developer
        |
        v
     GitHub
        |
        v
     CI
        |
        v
    Docker Image
        |
        v
     ECR
        |
        v
    CD / Jenkins
        |
        v
    Production

The big change is:

  Instead of moving individual application files around, you build a versioned application package and deploy that package.

======================================================================
10. WHAT IS EC2?
======================================================================

Now let's focus on the confusing part.

You asked:

  "Is EC2 like a desktop CPU?"

You are partly right, but EC2 is much more than a CPU.

Imagine you rent a physical building for your restaurant.

The building has:

  - CPU
  - RAM
  - Disk
  - Network
  - Operating System

That building is like an EC2 instance.

Technically:

  EC2 = a virtual server/computer provided by AWS.

Think of it as a computer that AWS owns physically, but you rent and use virtually.

                        EC2
            +---------------------------+
            |                           |
            |          CPU              |
            |          RAM              |
            |          Disk             |
            |          Network          |
            |          OS               |
            |                           |
            +---------------------------+

Your laptop is a computer.

An EC2 instance is also a computer/server from your application's point of view.

The main difference is that AWS provides and manages the underlying physical infrastructure.

======================================================================
11. RESTAURANT ANALOGY FOR EC2
======================================================================

Think:

  EC2 = Restaurant Building

For example:

                     EC2
           +-----------------------+
           |   Restaurant Building |
           |                       |
           |   CPU / RAM / Disk    |
           |                       |
           +-----------------------+

Now we can put Docker inside the building.

                     EC2
           +-----------------------+
           |       Linux OS        |
           |                       |
           |       Docker          |
           |                       |
           +-----------------------+

Docker can now run containers inside that EC2.

======================================================================
12. ONE EC2 CAN RUN MULTIPLE CONTAINERS
======================================================================

Absolutely.

For example:

    EC2 Server
        |
        +-- Container A -> PHP application
        |
        +-- Container B -> Redis
        |
        +-- Container C -> Nginx
        |
        +-- Container D -> Another application

So:

  One EC2 does NOT mean one container.

An EC2 is a computer.

Containers are workloads running on that computer.

How many containers you can run depends on CPU, RAM, storage, networking, application requirements, and architecture.

======================================================================
13. NOW KUBERNETES ENTERS THE STORY
======================================================================

Imagine your restaurant becomes extremely popular.

At first you have:

    100 customers

One restaurant is enough.

Then suddenly you have:

    100,000 customers

One application container isn't enough.

You need multiple copies:

    Container 1
    Container 2
    Container 3
    Container 4
    Container 5
    ...
    Container 20

Now a difficult question appears:

  Who manages all these containers?

Who notices if one crashes?

Who creates a replacement?

Who decides where each application copy should run?

Who increases the number of copies when traffic grows?

This is where Kubernetes comes in.

======================================================================
14. KUBERNETES = RESTAURANT MANAGER
======================================================================

Think of Kubernetes as the manager of a huge restaurant chain.

You tell Kubernetes:

  "I need 5 copies of my LiveSafe application running."

Kubernetes works to make that desired state happen.

                     Kubernetes
                    Restaurant Manager
                           |
                     "I need 5 copies"
                           |
              +------------+------------+
              |            |            |
              v            v            v
            Server       Server       Server
              |            |            |
          Containers   Containers   Containers

If one application copy crashes:

    Before:

    App 1  OK
    App 2  OK
    App 3  CRASHED
    App 4  OK
    App 5  OK

Kubernetes can detect that the desired number is no longer available and create a replacement.

    App 1  OK
    App 2  OK
    App 3  CRASHED
    App 4  OK
    App 5  OK
    App 6  <-- replacement

This is one of the fundamental ideas behind Kubernetes:

  You describe the desired state, and Kubernetes continuously works to maintain it.

======================================================================
15. WHERE DOES THE POD COME IN?
======================================================================

This is usually the most confusing concept.

Kubernetes normally doesn't work with a container as its main deployment unit.

It uses a concept called a Pod.

Think of a Pod as a small kitchen/work unit inside the restaurant building.

    EC2 / Server
        |
        +-- Pod
        |    |
        |    +-- Container
        |
        +-- Pod
        |    |
        |    +-- Container
        |
        +-- Pod
             |
             +-- Container

The most common Kubernetes setup is:

  1 Pod = 1 application container

For example:

    Pod 1
    +-- ls7-dashboard container

    Pod 2
    +-- ls7-dashboard container

    Pod 3
    +-- ls7-dashboard container

======================================================================
16. POD IS NOT THE SAME AS CONTAINER
======================================================================

This distinction is extremely important.

A container is:

  The actual isolated application process/environment.

A Pod is:

  Kubernetes' unit for running one or more closely related containers.

So:

    Pod
     |
     +-- Container

is the normal simple case.

But:

    Pod
     |
     +-- Container A
     |
     +-- Container B

is also possible.

======================================================================
17. CAN ONE POD CONTAIN MULTIPLE CONTAINERS?
======================================================================

YES.
----

For example:

                 Pod
           +-------------+
           |             |
           | Application |
           | Container   |
           |             |
           | Logging     |
           | Container   |
           |             |
           +-------------+

The second container could be a helper/sidecar.

For example:

    Pod
     |
     +-- Main application container
     |
     +-- Logging agent container

The main application runs the business logic.

The helper container performs a closely related supporting task.

However, for scaling an application, you normally don't put 5 copies of the application inside one Pod.

Instead, you normally create multiple Pods:

    Pod 1
    +-- Application container

    Pod 2
    +-- Application container

    Pod 3
    +-- Application container

    Pod 4
    +-- Application container

    Pod 5
    +-- Application container

======================================================================
18. CAN MULTIPLE PODS USE THE SAME DOCKER IMAGE?
======================================================================

YES - and this is extremely common.
-----------------------------------

Suppose ECR contains:

    ls7-dashboard:2.1.3

Kubernetes can run:

    Pod 1
    +-- Container <- ls7-dashboard:2.1.3

    Pod 2
    +-- Container <- ls7-dashboard:2.1.3

    Pod 3
    +-- Container <- ls7-dashboard:2.1.3

    Pod 4
    +-- Container <- ls7-dashboard:2.1.3

    Pod 5
    +-- Container <- ls7-dashboard:2.1.3

So:

  One image can be used to create many containers, and those containers can run in many Pods.

======================================================================
19. EC2 VS POD - THE KEY DIFFERENCE
======================================================================

This is probably the most important comparison for you.

Imagine:

    EC2 = Building
    Pod = Room/Kitchen inside the building
    Container = Worker/application running in that room

Therefore:

    EC2
     |
     +-- Pod
     |    |
     |    +-- Container
     |
     +-- Pod
          |
          +-- Container

An EC2 is a computer/server.

A Pod is a Kubernetes workload unit running on a Kubernetes node.

They are fundamentally different things.

======================================================================
20. THE REAL TECHNICAL RELATIONSHIP
======================================================================

When Kubernetes is running on AWS, a simplified picture can look like:

                             AWS
                              |
                  +-----------+-----------+
                  |                       |
                EC2 #1                  EC2 #2
              (Computer)              (Computer)
                  |                       |
            Kubernetes Node         Kubernetes Node
                  |                       |
            +-----+-----+           +-----+-----+
            |           |           |           |
           Pod         Pod         Pod         Pod
            |           |           |           |
        Container   Container   Container   Container

An EC2 instance can act as a Kubernetes worker node.

The Kubernetes cluster can have multiple nodes.

======================================================================
21. ONE EC2 CAN HAVE MULTIPLE PODS
======================================================================

Yes.

For example:

    EC2 #1
     |
     +-- Pod 1
     |    +-- Container
     |
     +-- Pod 2
     |    +-- Container
     |
     +-- Pod 3
     |    +-- Container
     |
     +-- Pod 4
          +-- Container

Therefore:

  One EC2 can run multiple Pods.

And because each Pod normally has one main container:

  One EC2 can indirectly run multiple containers through multiple Pods.

The actual number depends on resource limits and Kubernetes configuration.

======================================================================
22. ONE POD CAN HAVE MULTIPLE CONTAINERS
======================================================================

Also yes:

    Pod
     |
     +-- Container A
     |
     +-- Container B

But this is different from scaling.

For scaling:

    Pod 1
    +-- App container

    Pod 2
    +-- App container

    Pod 3
    +-- App container

For closely coupled helper functionality:

    Pod
     |
     +-- App container
     |
     +-- Helper/sidecar container

======================================================================
23. CAN ONE EC2 RUN CONTAINERS FROM THE SAME IMAGE?
======================================================================

Yes.

For example:

                     EC2
                      |
            +---------+---------+
            |         |         |
           Pod       Pod       Pod
            |         |         |
        Container  Container  Container
            |         |         |
            +---------+---------+
                      |
              Same image version
            ls7-dashboard:2.1.3

All three containers can use:

    ls7-dashboard:2.1.3

This is perfectly normal.

======================================================================
24. BUT KUBERNETES MAY SPREAD PODS ACROSS EC2S
======================================================================

Suppose you have:

    3 EC2 instances

Kubernetes may distribute your application:

                     Kubernetes
                          |
              +-----------+-----------+
              |           |           |
              v           v           v
           EC2 #1       EC2 #2       EC2 #3
              |           |           |
           Pod 1        Pod 3        Pod 5
           Pod 2        Pod 4

All five Pods could be running:

    ls7-dashboard:2.1.3

Why spread them?

Because if EC2 #1 dies:

    EC2 #1 [NO]

you can still have application copies on:

    EC2 #2 [OK]
    EC2 #3 [OK]

This is one reason Kubernetes is useful for highly available systems.

======================================================================
25. THE WHOLE PICTURE
======================================================================

Now let's combine everything.

                             AWS
                              |
                    +---------+---------+
                    |                   |
                  EC2 #1              EC2 #2
                (Computer)           (Computer)
                    |                   |
            +-------+-------+       +---+---+
            |               |       |       |
           Pod             Pod     Pod     Pod
            |               |       |       |
        Container       Container Container Container
            |               |       |       |
            +---------------+-------+-------+
                            |
                     Docker Image
                  ls7-dashboard:2.1.3
                            |
                            ^
                           ECR

And who manages the Pods?

                     Kubernetes
                     Manager
                         |
            +------------+------------+
            |                         |
          EC2 #1                    EC2 #2
            |                         |
           Pods                      Pods
            |                         |
        Containers                Containers

======================================================================
26. THE RESTAURANT ANALOGY - FINAL VERSION
======================================================================

Here is the mental model worth remembering:

+------------------+---------------------------------------------------+---------------------------------------------------+
| Technology       | Restaurant analogy                                | Simple meaning                                    |
+------------------+---------------------------------------------------+---------------------------------------------------+
| GitHub           | Recipe book                                       | Source code                                       |
| Dockerfile       | Instructions for preparing the restaurant package | How to build the image                            |
| Docker Image     | Ready-made food package/restaurant package        | Application blueprint/package                     |
| Docker Container | Running restaurant                                | Running application                               |
| ECR              | Company warehouse                                 | Stores Docker images                              |
| EC2              | Restaurant building                               | Virtual server/computer                           |
| Kubernetes Node  | Building managed by Kubernetes                    | Usually a server such as EC2                      |
| Pod              | Kitchen/work unit                                 | Kubernetes unit containing one or more containers |
| Container        | Worker/application                                | Actual running application process                |
| Kubernetes       | Restaurant manager                                | Manages Pods/workloads                            |
| CircleCI         | Quality checker                                   | Builds/tests/packages software                    |
| Jenkins          | Delivery manager                                  | Automates deployment                              |
+------------------+---------------------------------------------------+---------------------------------------------------+

======================================================================
27. THE ONE PICTURE TO REMEMBER
======================================================================

If you remember only one diagram, remember this:

                             AWS
                              |
                     +--------+--------+
                     |                 |
                   EC2 #1            EC2 #2
                 "Computer"         "Computer"
                     |                 |
              +------+------+     +----+----+
              |             |     |         |
            Pod 1         Pod 2  Pod 3     Pod 4
              |             |     |         |
          Container     Container Container Container
              |             |     |         |
              +-------------+-----+---------+
                            |
                       Same Docker
                          Image
                            |
                            v
                         AWS ECR

And above all of this:

                        Kubernetes
                      "Restaurant Manager"
                             |
                             v
                  Manages the Pods
                  across the EC2s

======================================================================
28. THE SIMPLEST POSSIBLE EXPLANATION
======================================================================

If someone asks you in an interview:

"What is EC2?"
--------------

You can say:

  EC2 is a virtual server/computer provided by AWS where applications and workloads can run.

"What is a container?"
----------------------

  A container is a running, isolated application environment created from a container image.

"What is a Pod?"
----------------

  A Pod is Kubernetes' basic workload unit that contains one or more closely related containers.

"What is Kubernetes?"
---------------------

  Kubernetes is a system that manages containerized workloads, including where they run, how many copies should run, and replacing failed workloads.

"Can one EC2 run multiple containers?"
--------------------------------------

  Yes.

"Can multiple Pods run on one EC2?"
-----------------------------------

  Yes.

"Can one Pod contain multiple containers?"
------------------------------------------

  Yes.

"Can multiple containers come from the same image?"
---------------------------------------------------

  Yes. That's very common.

======================================================================
29. ARCHITECT-LEVEL MENTAL MODEL
======================================================================

As someone moving toward Senior Technical Lead / Solution Architect, don't worry about memorizing every Kubernetes command yet.

Your most important mental model is:

    SOURCE CODE
        |
        v
      GitHub
        |
        v
        CI
        |
        v
    DOCKER IMAGE
        |
        v
       ECR
        |
        v
    DEPLOYMENT
        |
        v
    KUBERNETES
        |
        +----------------------+
        |                      |
        v                      v
      EC2/Node               EC2/Node
        |                      |
       Pods                   Pods
        |                      |
    Containers              Containers
        |                      |
        +----------+-----------+
                   |
                   v
              Application
                   |
                   v
                 Users

The architectural question is no longer just:

  "How do I run PHP?"

It becomes:

  "How do I package, deploy, scale, distribute, monitor, secure, and reliably operate my application?"

That is the level at which Docker + Kubernetes knowledge becomes valuable for a Technical Lead / Solution Architect.
