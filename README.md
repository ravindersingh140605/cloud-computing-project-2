# Cloud Computing Project 2: Containerize and Run Locally# Cloud Computing Project 2: Containerize and Run Locally

## 1. Project Overview

This project demonstrates how to build and run a web application locally using Docker and Docker Compose. It also includes an AWS SAM function that can be tested locally without deploying it to AWS.
# Cloud Computing Project 2: Containerize and Run Locally# Cloud Computing Project 2: Containerize and Run Locally
The project compares single-stage and multi-stage Docker builds and demonstrates how to run a web service alongside a Redis container.

## 2. Technologies Used

* Docker and Docker Desktop
* Docker Compose
* Node.js and npm
* Nginx Alpine
* Redis
* AWS SAM CLI
* Git and GitHub
* Ubuntu on WSL

## 3. Project Structure

* `Dockerfile` — Multi-stage Docker build.
* `Dockerfile.single` — Single-stage Docker build for comparison.
* `compose.yaml` — Configuration for the web and Redis services.
* `index.html` — Sample web page.
* `package.json` — Application information and build script.
* `package-lock.json` — Records npm dependency information.
* `local-fn/` — AWS SAM application for local testing.
* `.dockerignore` — Excludes unnecessary files from Docker build contexts.
* `.gitignore` — Excludes selected generated files from Git.

## 4. Build the Docker Images

Build the single-stage image:

```bash
docker build -f Dockerfile.single -t demo:single .
```

Build the multi-stage image:

```bash
docker build -t demo:multi .
```

Compare the image sizes:

```bash
docker images demo
```

## 5. Run the Application with Docker Compose

Start the services:

```bash
docker compose up -d
```

Check the running containers:

```bash
docker compose ps
```

Check whether the website responds:

```bash
curl -I http://localhost:8080
```

Check the Redis service:

```bash
docker compose exec cache redis-cli ping
```

The expected Redis response is:

```text
PONG
```

View the web container logs:

```bash
docker compose logs web
```

Check the Nginx version:

```bash
docker compose exec web sh -c 'nginx -v'
```

Stop and remove the Compose containers:

```bash
docker compose down
```

## 6. Test the AWS SAM Function Locally

Move into the SAM project:

```bash
cd ~/t02app/local-fn
```

Run the function:

```bash
sam local invoke HelloWorldFunction
```

Test it using the sample event:

```bash
sam local invoke HelloWorldFunction -e events/event.json
```

The successful test returns a response containing status code `200` and the message `hello world`.

Return to the main project directory:

```bash
cd ~/t02app
```

## 7. Docker Image Comparison

The recorded Docker image results were:

| Image                        | Disk Usage | Content Size |
| ---------------------------- | ---------: | -----------: |
| Single-stage (`demo:single`) |    1.65 GB |       413 MB |
| Multi-stage (`demo:multi`)   |    93.6 MB |      26.3 MB |

The multi-stage image was considerably smaller because the final image contains the finished website served by Nginx rather than the full Node.js build environment.

## 8. Official References

* Docker multi-stage builds: https://docs.docker.com/build/building/multi-stage/
* Docker Compose startup order: https://docs.docker.com/compose/how-tos/startup-order/
* AWS SAM installation: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html

* AWS SAM local testing: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli-local.html
* Docker Compose configuration: https://docs.docker.com/reference/cli/docker/compose/config/
