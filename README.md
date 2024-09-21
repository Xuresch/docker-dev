
# Docker Development Tools Repository

This repository provides a set of Docker containers to facilitate development workflows using Docker as devtools. The project includes predefined containers for running `npm`, `npx`, and a development server.

## Project Structure

```bash
docker-dev/
├── Dockerfiles
│   ├── mynpm
│   │   └── Dockerfile
│   ├── mynpx
│   │   └── Dockerfile
│   └── rundev
│       └── Dockerfile
├── README.md
└── docker-compose.yml
```

### Dockerfiles

Each directory under `docker-dev/Dockerfiles/` contains a `Dockerfile` specific to different development tasks:

- **mynpm**: A container for running `npm` commands.
- **mynpx**: A container for running `npx` commands.
- **rundev**: A container to set up and run a development server.

---

## Docker Setup

### Prerequisites

Ensure you have the following installed:

- [Docker](https://docs.docker.com/get-docker/)
- [Docker Compose](https://docs.docker.com/compose/install/)

---

## Dockerfiles Overview

### mynpm Dockerfile

This container is used for running `npm` commands within the project directory.

```Dockerfile
# Dockerfile for mynpm
FROM node:22-alpine3.19

WORKDIR /app

ENTRYPOINT [ "npm" ]
```

### mynpx Dockerfile

This container is used for running `npx` commands within the project directory.

```Dockerfile
# Dockerfile for mynpx
FROM node:22-alpine3.19

WORKDIR /app

ENTRYPOINT [ "npx" ]
```

### rundev Dockerfile

This container sets up a development server with `npm run dev`, copying the `package.json` and source files into the container, installing dependencies, and exposing port 3000 for development.

```Dockerfile
# Dockerfile for rundev
FROM node:22-alpine3.19

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 3000

CMD [ "npm", "run", "dev" ]
```

---

## Docker Compose

The `docker-compose.yml` file sets up services for running `npm` and `npx` commands, using the predefined Dockerfiles:

```yaml
services:
  mynpx:
    build: ./Dockerfiles/mynpx/.
    image: mynpx:latest
    container_name: mynpx
    tty: true
    stdin_open: true
    restart: always

  mynpm:
    build: ./Dockerfiles/mynpm/.
    image: mynpm:latest
    container_name: mynpm
    tty: true
    stdin_open: true
    restart: always
```

---

## Usage

### Step 1: Build the Docker Images

Before running the containers, the Docker images for `mynpm` and `mynpx` must be built using the following command:

```bash
docker-compose -f docker-dev/docker-compose.yml build
```

### Step 2: Running npm and npx commands

You can now use the following commands to run `npm` or `npx` inside the containers.

#### Run npx (e.g., creating a new Next.js project)

```bash
docker-compose -f docker-dev/docker-compose.yml run --volume /project/to/:/app --rm mynpx create-next-app@latest
```

#### Run npm (e.g., installing dependencies)

```bash
docker-compose -f docker-dev/docker-compose.yml run --volume /project/to/:/app --rm mynpm install
```

You can also use other npm commands like `npm init` or any `npm` related task by substituting the command in the last argument.

---

## rundev Container for Development Server

### Step 3: Build and Run the Development Server

First, copy the files in the `rundev` directory into your project repository. Then, build the development server container using the following command:

```bash
docker build . -t {your-project-tag}
```

### Step 4: Run the Development Server

To run the development server, use the following command:

```bash
docker run -v /project/to/src:/app/src --name {container-name} -p 3000:3000 --rm -it {your-project-tag}
```

This will run the server on port `3000`, and the container will automatically update as you make changes to the files in the `/project/to/src` directory.

---

## .dockerignore

A `.dockerignore` file is used to exclude certain files from being copied into the Docker image. This file should be placed in the root of your project:

```
node_modules
Dockerfile
.gitignore
```

This ensures that unnecessary files, such as local `node_modules`, are not included in the Docker build context.

---

## Summary of Commands

1. **Build images for `mynpm` and `mynpx`:**

    ```bash
    docker-compose -f docker-dev/docker-compose.yml build
    ```

2. **Run npx commands (e.g., create-next-app):**

    ```bash
    docker-compose -f docker-dev/docker-compose.yml run --volume /project/to/src:/app --rm mynpx create-next-app@latest
    ```

3. **Run npm commands (e.g., install dependencies):**

    ```bash
    docker-compose -f docker-dev/docker-compose.yml run --volume /project/to/src:/app --rm mynpm install
    ```

4. **Build and run the development server:**

    ```bash
    docker build . -t {your-project-tag}
    docker run -v /project/to/src/src:/app/src --name {container-name} -p 3000:3000 --rm -it {your-project-tag}
    ```

This documentation should cover the basic usage of Docker as a development tool in your project.
