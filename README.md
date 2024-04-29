
![docker-logo-blue](https://github.com/Sagar-Chowdhury/Docker-Theory/assets/76145064/4ca3fffa-4239-4b8d-85a2-500f3dc6c626)

**Docker 🐳 – Solving Development Environment Headaches**

**Problem Statement**

* Replicating development environments across machines can be a pain, especially when dealing with different configurations and dependencies.
* Onboarding new developers gets tougher as they have to set up the entire development environment from scratch.

**Introduction to Docker**

Docker is here to save the day! It's a platform designed to make it super easy to build, package, and run your applications consistently in any environment.

**Key Components**

1. **Docker CLI (Command Line Interface):**  Your trusty tool for interacting with Docker. Use it to control containers, images, and more.
2. **Docker Engine:** The heart of Docker, responsible for running containers and managing images.
3. **Docker Hub:** A massive online repository filled with pre-built Docker images.

**Installation**

* Head to the official Docker website and download Docker Desktop for your operating system (includes everything you need - Engine, CLI, etc.): [https://www.docker.com/get-started](https://www.docker.com/get-started)

**Docker CLI: Your Swiss Army Knife**

| Command                | Description                                          |
| -----------------------| -----------------------------------------------------|
| `docker version`       | Check your installed Docker versions                  |
| `docker pull`          | Grab an image from a registry like Docker Hub         |
| `docker run`           | Create and start a new container from an image        |
| `docker ps`            | List all running containers                          |
| `docker ps -a`         | List all containers (running and stopped)            |
| `docker stop`          | Gracefully stop a running container                  |
| `docker kill`          | Forcefully stop a running container                   |
| `docker exec`          | Run a command inside a running container             |
| `docker images`        | Show the images you have locally                     |
| `docker rmi`           | Delete an image                                      |
| `docker build`         | Build a new image from a Dockerfile               |

**Example:**

```bash
docker run -it ubuntu 
```

* **-it:** Gives you an interactive session inside the container.
* **ubuntu:** The image to use. If you don't have it locally, Docker will download it.

**Images vs. Containers: The Analogy**

* **Image:** Think of it as a class definition in programming. It's the blueprint for what your application *could* be. Static and unchanging.
* **Container:** Like an object created from that class. A running instance of an image, where your application lives. You can run multiple containers from the same image.

**Port Mapping: Opening Doors**

To access your application from your machine, map your container's port to a port on your host:

```bash
docker run --publish 8080:80 <image_name> 
```
(Access your app on your browser at http://localhost:8080)

**Environment Variables: Flexible Configuration**

Pass environment variables at runtime:

```bash
docker run -e MYVAR1="value" --env MYVAR2=foo ubuntu bash
```

**Dockerizing a Node.js Application**

[Using Docker init these are created automatically](https://docs.docker.com/language/nodejs/containerize/)

1. **Dockerfile:** Instructions for building your image.

   ```dockerfile
   FROM node:latest # Base image
   WORKDIR /app     # Working directory inside container
   COPY package.json . 
   RUN npm install   
   COPY . .          # Copy your project files
   EXPOSE 8080       # Expose the port
   CMD ["node", "app.js"]  
   ```

2. **.dockerignore:** Excludes unnecessary files from your build.

3. **Build and Run:**

   ```bash
   docker build -t my-node-app .
   docker run -it -p 8080:8080 my-node-app
   ```

**The Docker Daemon: Your Container Conductor**

* **The Backbone:** The Docker daemon is a persistent background process running on your host machine. It's the powerhouse responsible for the heavy lifting of Docker's magic.
* **Key Responsibilities:**
    * **Image Management:** Building, storing, and pulling/pushing images to registries.
    * **Container Lifecycle:**  Launching, running, stopping, and removing containers.
    * **Networking:** Creating and configuring virtual networks for inter-container communication.
    * **Volume Management:**  Setting up persistent data volumes for your containers.
    * **Security:** Enforcing security features and isolation within the Docker ecosystem.
* **Communication Gateway:**  It's also in charge of the Docker API. This RESTful API is what you interact with using the Docker CLI or other tools.

**Docker `run` vs. `exec`: Understanding the Nuances**

| Feature  | `docker run`                                         | `docker exec`                                                |
|----------|----------------------------------------------------------|--------------------------------------------------------------|
| Purpose  | **Creates and starts a new container** from an image.    | **Executes a command within an already running container.**  |
| New Layer| Creates a new writable top layer in the container.       | Doesn't change the existing container's layers.              |
| Isolation| Provides a separate, isolated environment.                | Operates within the existing container's environment.         |
| Use Cases| **Initial startup** of your application in a container.  | **Troubleshooting, inspecting, or modifying** running processes.|

**Illustrative Example**

* **`docker run -it ubuntu bash`:** 
    * Pulls the `ubuntu` image if missing.
    * Creates a brand-new container from that image.
    * Starts an interactive bash session within the newly created container. 

* **`docker exec -it <running_container_id> ls -la`:**
    * Lists the directory contents (using `ls -la`) inside an already running container.

**Key Points**

* `docker run` is typically used to start your main application's processes inside a container.
* `docker exec`  is incredibly useful for debugging, running auxiliary commands, or interacting with a running application within its container.

**Docker Compose: Your Multi-Container Maestro**

**Why Docker Compose?**

While Docker is fantastic for running single containers, real-world applications often involve multiple interconnected services:

* Web server (e.g., Nginx)
* Database (e.g., MySQL, PostgreSQL)
* Cache (e.g., Redis)
* Message queue  (e.g., RabbitMQ)

Managing these individually with Docker commands can get very complex! Docker Compose steps in to orchestrate multi-container setups with grace.

**Key Benefits**

1. **Single Configuration:** Define all your services, their dependencies, networks, and volumes within a single YAML file (`docker-compose.yml`). This keeps your project organized.
2. **Environment Replication:**  Easily spin up identical copies of your entire application stack across different machines, simplifying development, testing, and deployment.
3. **Collaboration:**  Share a `docker-compose.yml` file, and others can quickly bring up your entire application, dependencies and all.

**Basic Docker Compose Commands**

| Command              | Description                                                  |
| ---------------------| ------------------------------------------------------------ |
| `docker-compose up`  | Creates and starts all the containers defined in your `docker-compose.yml` file.   |
| `docker-compose up -d` | Same as above, but runs containers in detached mode (background). |
| `docker-compose down` | Stops and removes containers, networks, and volumes created by Compose. |
| `docker-compose ps`  | Lists the containers managed by Compose.                     |
| `docker-compose logs` | View the aggregated output logs of your services.             |
| `docker-compose build` | Builds or rebuilds images defined in your Compose file.      |

**Illustrative `docker-compose.yml`**

```yaml
version: '3'  # Compose file version
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
  database:
    image: mysql:5.7
    environment:
      MYSQL_ROOT_PASSWORD: mysecretpassword
    volumes:
      - db_data:/var/lib/mysql 
volumes:
  db_data: 
```

**Let's Break it Down**

* It defines two services: `web` (Nginx) and `database` (MySQL).
* `ports` maps host port 8080 to container port 80 for the web service.
* `environment` sets up an environment variable for the MySQL container.
* `volumes` creates a named volume `db_data` for persistent database storage.

**Docker Networking**

[Documentation](https://docs.docker.com/network/)
[Network Drivers](https://docs.docker.com/network/drivers/)

| Network Type | Description                                                   | Use Cases                                             | Syntax Example                                       |
|--------------|---------------------------------------------------------------|-------------------------------------------------------|------------------------------------------------------|
| Bridge       | Default network; connects containers on the same host.         | Single-host applications, isolated microservices      | Implicit for most `docker run` commands              |
| Host         | Container shares host's network namespace (no isolation).     | Special cases needing direct host network access(**no port mapping** needed)      | `docker run --network host ...`                      |
| Overlay      | Spans multiple hosts; requires external key-value store.    | Distributed applications across multiple machines     | `docker network create -d overlay my-network ...`    |
| Macvlan      | Assigns MAC addresses to containers; bypasses host network. | Low-latency, needs network infrastructure support    | `docker network create -d macvlan ...`               |
| None         | **No networking**; complete isolation within the container.       | Highly specialized, maximum security scenarios        | `docker run --network none ...`                      |

**Additional Notes:**

* **Port Mapping:** Use `-p <host_port>:<container_port>`  with `docker run` to expose ports.
* **Service Discovery:** Containers within the same network can typically find each other by service name.

**Comparison Table:**

| Feature          | Normal Bridge Network | User-defined Bridge Network |
|------------------|------------------------|------------------------------|
| Creation         | Implicit                | Explicit (using `docker network create`) |
| Configuration     | Limited                 | Advanced (IP range, DNS, etc.) |
| Sharing          | All containers share   | Isolated by default           |
| Customization    | Limited                 | Highly customizable            |
| Use Cases         | Basic deployments       | Complex deployments, specific network requirements |

**Example:**

* **Normal Bridge Network:**

```bash
docker run -d --name webserver nginx
```

This creates a container named "webserver" on the default bridge network.

* **User-defined Bridge Network:**

```bash
docker network create my-app-network
docker run -d --name webserver --network my-app-network nginx
```

Here, we create a custom network named "my-app-network" and then run the "webserver" container attached to that network.

**In essence:**

* Use the normal bridge network for simple setups where basic container communication is sufficient.
* Leverage user-defined bridge networks for more complex deployments requiring specific configurations, isolation, or customization.



**What are Docker Volumes?**

* Docker volumes are specially designated storage locations on the host machine that are designed to persist data independently of a container's lifecycle. 
* They are managed directly by Docker, providing a more efficient and flexible way to handle data than storing it within a container's writable layer.

**Key Use Cases for Docker Volumes**

1. **Data Persistence:**
   * **Databases:** Databases like MySQL or PostgreSQL must store data in a way that survives container restarts. Volumes ensure data isn't erased if a container crashes or you upgrade the database image.
   * **Configuration files:**  If your application needs to persist configuration files, volumes guarantee they're not lost within the container.

2. **Data Sharing and Collaboration:**
    * **Multiple Containers:**  Several containers can simultaneously mount the same volume, enabling them to share and collaborate on data.
    * **Microservices:**  In microservice architectures, different services can communicate and work together via shared volumes.

3. **Performance:**
    * In some cases, accessing data directly through a volume on the host filesystem can be faster than accessing it through a container's layers.

4. **Backup and Migration**
    * Since volumes reside on the host machine, they're easier to back up or migrate than data within the container's file system. 

**Why Do We Need Docker Volumes?**

* **Persistence beyond container lifecycle:** When a container is stopped or removed, any changes made to its file system are lost. Volumes provide a way to store data that needs to outlive individual containers.

* **Sharing data across containers and services:**  Volumes facilitate smooth data sharing and collaboration between different parts of your application

* **Separation of concerns:** Volumes let you separate application code (stored in the image) from persistent data, improving maintainability and allowing for easy image updates.

**Mounting Existing Volumes**

| Command                                       | Description                                            | Example                                                                  |
|-----------------------------------------------|--------------------------------------------------------|----------------------------------------------------------------------|
| `docker run -v <volume-name>:/path/in/container ...` | Mounts an existing named volume to the container.  | `docker run -v my-data-volume:/var/lib/mysql -d mysql` |
| `docker run -v <host-path>:/path/in/container ...` | Mounts a directory from the host machine to the container.  | `docker run -v /data/on/host:/data/in/container -d my-app` |

```bash
docker run -it -v D:\SampleDockerFolder:/home/sharedFolder busybox
```
![image](https://github.com/Sagar-Chowdhury/Docker-Notes/assets/76145064/6016a1b7-262d-47a4-bf88-4ff6bc795998)


* For a detailed, comprehensive list of options, refer to the official Docker documentation: [https://docs.docker.com/storage/volumes/](https://docs.docker.com/storage/volumes/)
* **Remember:** Volumes should be used for data that needs to persist beyond the life of individual containers.








