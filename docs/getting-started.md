---
sidebar_position: 1
title: Getting Started
---

Let's get LLMine up and running **in less than 10 minutes**.

## Prerequisites

1. We are assuming you are on a UNIX like terminal (Linux / Mac / WSL 2.0)
2. Docker works from command line
    1. For Mac follow https://docs.docker.com/desktop/install/mac-install/
    2. For Linux follow https://docs.docker.com/desktop/install/linux-install/
    3. For Windows follow https://docs.docker.com/desktop/install/windows-install/
3. Docker Compose is installed
    1. Will be installed with Docker Desktop if you have followed the above guides
4. Git is installed
    1. https://git-scm.com/book/en/v2/Getting-Started-Installing-Git

## Installing LLMine Core

### Clone the Repository

```bash
$ git clone https://github.com/LLMine/llmine_core.git
```

### Edit the .sample.env file

Edit the `.sample.env` file in the root directory. You will need to provide the following.
1. `SECRET_KEY` value - Just generate a random UUID4 string and paste in the env file
    1. Use https://www.uuidgenerator.net/version4
2. `OPENAI_API_KEY` value - You will need to obtain an OpenAI API Key
    1. Sign Up / Login in https://platform.openai.com
    2. Click on the Account Icon > View API Keys > Create new secret key > Copy the key
    3. Paste it in the `.sample.env` file in the right place.
3. You may also want to change the `DJANGO_SUPERUSER_USERNAME` and `DJANGO_SUPERUSER_PASSWORD` especially if you are hosting it over a public network.

### Start the Docker Containers
You may or may not need to `sudo` access, depending on how docker was installed.

```bash
$ sudo docker-compose up -d --build
```

The above should start all the docker containers in some time. You should be able to verify it by running
```bash
$ sudo docker ps
```
The above should print an output like:
```
CONTAINER ID   IMAGE                            COMMAND                  CREATED         STATUS         PORTS                NAMES
c98e21a87d67   nginx:latest                     "/docker-entrypoint.…"   4 seconds ago   Up 2 seconds   0.0.0.0:80->80/tcp   llmine_core-llmine_core_nginx-1
2575bb3e1991   llmine_core-llmine_core          "/home/llmine_core/d…"   4 seconds ago   Up 3 seconds                        llmine_core-llmine_core-1
d6d2ce05ea80   llmine_core-llmine_core_celery   "/home/llmine_core/d…"   4 seconds ago   Up 3 seconds                        llmine_core-llmine_core_celery-1
1630486f7826   llmine_core-llmine_core_db       "docker-entrypoint.s…"   5 seconds ago   Up 3 seconds   5432/tcp             llmine_core-llmine_core_db-1
09084790de22   redis:latest                     "docker-entrypoint.s…"   5 seconds ago   Up 4 seconds   6379/tcp             llmine_core-llmine_core_redis-1
```

### Verify the installation

Navigate to (http://localhost) now. You should see a page like this.

<img src="/assets/screenshots/welcome-page.png" />

### Login to the Admin Panel

Click on the "Login to Admin" button on the welcome page or navigate to (http://localhost/admin). You should see a login page. Enter the username and password as set in the `.sample.env` file via `DJANGO_SUPERUSER_USERNAME` and `DJANGO_SUPERUSER_PASSWORD` variables. If you haven't updated the credentials, it will be `defaultadmin` and `defaultpassword@1234`.

<img src="/assets/screenshots/admin-home.png" />

## Congratulations

Hurray! You have successfully started all LLMine services. You should be able to follow along our Tutorial from here.