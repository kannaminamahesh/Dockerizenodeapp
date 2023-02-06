# Dockerizing-a-NodeJS-web-app
<p align="center">
    <img
      alt="Node.js"
      src="nodeJsDocker.png"
      width="400"
    />
</p>
<hr>

Create a simple nodeJs application and deploy it onto a docker container.

1. Create a working directory
    > mkdir <working_directory_name>
  
2. Running this command in working directory will initialize your project
    > npm init
  
This will create a **package.json** file in the folder, that file contains app dependency packages.

Replace the following code of package.json

```js
  // package.json

  {
    "name": "docker_web_app",
    "version": "1.0.0",
    "description": "Node.js deploy on Docker container",
    "author": "cmuth001@odu.edu",
    "main": "server.js",
    "scripts": {
      "start": "node server.js"
    },
    "dependencies": {
      "express": "^4.16.1"
    }
  }
```

2. Running this command will install all the dependencies from package.json 
    > npm install
3. Lets create a **server.js** file that defines a web-app using an Express framework.

  
 ```js
    // server.js
    'use strict';
    var express = require('express');
    var app = express();
    app.get('/', function (req, res) {
      res.send('Hello World!');
    });
    app.listen(3000, function () {
      console.log('Example app listening on port 3000!');
    });

 ```
 
4. Lets test the application, run the below command
    > node server.js
  
  If you followed the  above steps on your system, you will see the same output as below image: [http://localhost:3000/](http://localhost:3000/)
  
  <p align="center">
    <img
      alt="Node.js"
      src="image1.png"
      width="400"
    />
  </p>
      
  Now node.js app is running successfully.
   
Lets try running the same node.js application running on the docker container. To run the application on the docker conatiner we need a docker image. 

First, we will create a docker image for the application.
   
5. Create a **Dockerfile**
    > touch Dockerfile
6. Dockerfile should look like this
```
FROM node:10
# Create app directory
WORKDIR /usr/app

# Install app dependencies
# A wildcard is used to ensure both package.json AND package-lock.json are copied
# where available (npm@5+)
COPY package*.json ./

RUN npm install
# If you are building your code for production
# RUN npm ci --only=production

# Bundle app source
COPY . .
EXPOSE 3000
CMD [ "node", "server.js" ]

```
7. Create **.dockerignore** file with following content
 
```
node_modules
npm-debug.log
```
   This will prevent from copying onto docker image.
    
8. Building Docker image
    > docker build -t node-web-app .
    
    <p align="center">
        <img
          alt="Node.js"
          src="image2.png"
          width="400"
        />
      </p>
9. Check the Docker images
    > docker images
    
    <p align="center">
        <img
          alt="Node.js"
          src="image3.png"
          width="400"
        />
      </p>
10. Run the docker image
    > docker run -p 49160:3000 -d node-web-app
 
11. Get the container id
    > docker ps
    
    <p align="center">
        <img
          alt="Node.js"
          src="image4.png"
          width="400"
        />
      </p>
      
12. Lets know where it is running on
    > docker logs <container_id>
    
```
output: 
    Example app listening on port 3000!
```
13. If you followed the  above steps on your system, you will see the sam output as below image: [http://localhost:49160/](http://localhost:49160/)
  
  <p align="center">
        <img
          alt="Node.js"
          src="image5.png"
          width="400"
        />
      </p>
    
// additional docker commands

#Step 1: Building an image
#Syntax: docker build -t <image-name> <location of dockerfile>
-------------------------------------------------------------------
docker build -t my-httpd .

#Step 2: List all the images
#Syntax: docker images
-------------------------------------------------------------------
docker images

#Step 3: Inspect the Image
------------------------------------------------------------------
docker inspect <image-id>

#Step 4: Running a container from the image
#Syntax: docker run -itd --name <container-name> -p <host-port>:<port in container> image-name:tag
# note in the above syntax:
# -d : represents (detached mode), note that if you dont run this in detached mode, the life of the container will be the life of the terminal in which you are executing it.
# -p : represents the host-port to container-port mapping, if you substitute it with -P you will get a random port allocated by docker
# --name : represents the name of the container 
-------------------------------------------------------------------
docker run -itd --name my-http-container-1 -p 5555:80 my-httpd:latest
docker run -itd --name my-http-container-2 -p 5556:80 my-httpd:latest
docker run -itd --name my-http-container-with-random-port -P my-httpd:latest

#note if you run this it will NOT run in detached mode.
docker run -it --name my-http-container-not-detached-mode -p 5557:80 my-httpd:latest

Step 5: View all the containers
-------------------------------------------------------------------
# Shows all the containers which are running
docker ps 

#Shows all the containers stopped and running
docker ps -a

Step 6: Inspect the container and image
-------------------------------------------------------------------
docker inspect <container-id>

Step 7: View Container logs
---------------------------d1fdb4649c31----------------------------------------

# View the logs of the container
docker logs <container-id>

# Tail the logs of the container
docker logs -ft <container-id>

Step 8: Stop the container
-------------------------------------------------------------------
docker stop <container-id>

Step 9: Stop the container
-------------------------------------------------------------------
docker start <container-id>

Step 10: Logging into the container
-------------------------------------------------------------------
#Note: The container must be started before we can do this.
docker exec -it <container-id> /bin/bash
ctrl c+d to log out of a container.

Step 11: Remove all the containers and images
-------------------------------------------------------------------
#Note: To remove an image the corresponding container built from that image will need to be removed.

#Remove a specific container
docker rm <container-id>

#Remove all containers
docker rm $(docker ps -a -q)

# remove image (note: no containers for this image should be running)
docker rmi <image-id>

# remove all images
docker rmi $(docker images -q)

# delploy a image and also link it to an existing container
docker run -d --name=clickcounter --link redis:redis -p 8085:5000 kodekloud/click-counter


# backup data from mysql to data or vice versa when db crashes
docker run -v /opt/data:/var/lib/mysql -d --name mysql-db -e MYSQL_ROOT_PASSWORD=db_pass123 mysql

# networks
docker network ls

# check for subnet or container's network'
docker inspect containerid or networkid

# specify network for a container
docker run -itd --name nameofcontainer --network nameofexistingnetworks imagename

#Create a new network named wp-mysql-network using the bridge driver. Allocate subnet 182.18.0.1/24. Configure Gateway 182.18.0.1
docker network create --driver bridge --subnet 182.18.0.1/24 --gateway 182.18.0.1 wp-mysql-network

#Deploy a mysql database using the mysql:5.6 image and name it mysql-db. Attach it to the newly created network wp-mysql-network, Set the database password to use db_pass123. The environment variable to set is MYSQL_ROOT_PASSWORD
docker run -d -e MYSQL_ROOT_PASSWORD=db_pass123 --name mysql-db --network wp-mysql-network mysql:5.6

#Deploy a web application named webapp using the kodekloud/simple-webapp-mysql image. Expose the port to 38080 on the host.

The application makes use of two environment variable:
1: DB_Host with the value mysql-db.
2: DB_Password with the value db_pass123.
Make sure to attach it to the newly created network called wp-mysql-network.

Also make sure to link the MySQL and the webapp container.
docker run --network=wp-mysql-network -e DB_Host=mysql-db -e DB_Password=db_pass123 -p 38080:8080 --name webapp --link mysql-db:mysql-db -d kodekloud/simple-webapp-mysql    
 
