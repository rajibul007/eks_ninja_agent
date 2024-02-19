Create a new Docker Image, Run as Container and Push to Docker Hub 

## Pre-requisite Step
- Create your Docker hub account. 
- https://hub.docker.com/

## Step-2: Create Dockerfile and copy my portfolio page
- **Dockerfile**
```
# Use the official Nginx base image
FROM nginx

# Set the working directory inside the container
WORKDIR /usr/share/nginx/html

# Copy the contents of the local directory into the container
COPY app .

```

## Step-3: Build Docker Image & run it
```
docker build -t rajibul/my_portfolio:v1 .
docker run --name my_portfolio -p 80:80 -d rajibul/my_portfolio:v1

Replace your docker hub account Id
docker build -t <your-docker-hub-id>/my_portfolio:v1 .
docker run --name mynginx1 -p 80:80 -d <your-docker-hub-id>/my_portfolio:v1
```

## Step-4: Tag & push the Docker image to docker hub
```
docker images
docker tag rajibul/my_portfolio:v1 rajibul/my_portfolio:v1-release
docker push rajibul/my_portfolio:v1-release

Replace with  docker hub account Id
docker tag <your-docker-hub-id>/my_portfolio:v1 <your-docker-hub-id>/my_portfolio:v1-release
docker push <your-docker-hub-id>/my_portfolio:v1-release
```
## Step-5: Verify the same on docker hub
- Login to docker hub and verify the image we have pushed
- Url: https://hub.docker.com/repositories