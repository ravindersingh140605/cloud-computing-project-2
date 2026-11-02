# Technical Rationale: Containerize and Run Locally

## 1. Choice of Docker Images

For this project, I used a multi-stage Docker build to separate the application build process from the final web server. The first stage uses Node.js to install the required packages and build the website. The second stage uses Nginx Alpine to serve the finished website.

I chose this approach because the final container does not need the complete Node.js development environment. It only needs the generated website and a web server to display it. This makes the final image smaller and reduces the amount of unnecessary software included in the container.

## 2. Image Size Comparison

I built both single-stage and multi-stage Docker images and checked their sizes using the Docker command line.

The single-stage image, `demo:single`, showed a disk usage of 1.65 GB and a content size of 413 MB. The multi-stage image, `demo:multi`, showed a disk usage of 93.6 MB and a content size of 26.3 MB.

These results show that the multi-stage image is considerably smaller. The single-stage image contains Node.js, the build tools, and the application environment. In comparison, the multi-stage image uses Nginx Alpine to serve the completed website without including the full Node.js build environment.

The comparison demonstrates how multi-stage builds can reduce the size of the final image. Docker's reported disk usage and content size are different measurements, so they should be considered separately.

## 3. Separation of Services

I used Docker Compose to run two services: `web` and `cache`. The web service builds the application from the project's Dockerfile and makes it available through port 8080 on the local computer. The cache service uses the Redis 7 Alpine image.

Keeping the web server and Redis in separate containers makes the setup easier to manage. Each service has its own responsibility, and the services can be started and stopped together through Docker Compose.

I also configured a Redis health check using `redis-cli ping`. The web service uses `depends_on` with the `service_healthy` condition so that Compose waits for Redis to become healthy before starting the web service. This improves startup coordination, although it does not mean the website itself depends on Redis for its static content.

## 4. Local AWS SAM Testing

The project also includes an AWS SAM application. I used `sam local invoke HelloWorldFunction` to test the function on my computer without deploying it to AWS.

The function returned status code 200 and the message `hello world`. I also tested the function using the sample event file. This demonstrated how a serverless function can be tested locally during development.

## 5. Conclusion

This project helped me understand multi-stage Docker builds, container image sizes, Docker Compose services, health checks, and local AWS SAM testing. The image comparison showed that using a separate build stage and a smaller runtime image can make the final container more lightweight.

## References

* Docker. Multi-stage builds: https://docs.docker.com/build/building/multi-stage/
* Docker. Compose startup order: https://docs.docker.com/compose/how-tos/startup-order/
* AWS. Installing the AWS SAM CLI: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/install-sam-cli.html
* AWS. Testing with the AWS SAM CLI locally: https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/using-sam-cli-local.html
* Docker. Compose configuration: https://docs.docker.com/reference/cli/docker/compose/config/
