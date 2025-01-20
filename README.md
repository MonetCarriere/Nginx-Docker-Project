## Project Overview

This project accomplishes three main objectives:

1. **Create a backend server with three dockerized instances**: 
   - A backend server is created using NodeJS (with the Express framework) 
which serves a simple HTML file.
   - Then three instances of the Node server application are created as 
Docker containers that run locally.

2. **Install Nginx and set it as a reverse proxy and load balancer**: 
   - Nginx is installed and configured to act as a reverse proxy, 
distributing incoming requests among the three app instances to achieve 
load balancing.

3. **Secure a HTTPS connection**: 
   - Set the Nginx proxy to enable HTTPS and configure it with our self 
signed TLS Certificate to ensure secure communication.



## How The Project Was Done (step by step):

<img width="391" alt="Screenshot 2025-01-19 at 1 13 43 PM" src="https://github.com/user-attachments/assets/1555f0c8-692c-4a8a-81cb-35a236541945" />




### - I created a **index.html** file and a folder named **images** that contains all of the images for the webpage.

The webpage looks like this:
<img width="1427" alt="nginx-webpage" src="https://github.com/user-attachments/assets/0e6ba98d-7be7-46a7-b520-35f450bb7256" />

### - Then, I made a **server.js** file and created a backend server with express that serves the html page to the web browser on port 3000.

<img width="560" alt="Screenshot 2025-01-19 at 12 57 17 PM" src="https://github.com/user-attachments/assets/246b6a1c-1c9e-460d-b42f-86797638b47a" />

### - Then, I generated a package.json file by running the command: 
### **npm install**

### - Then, I ran the express server with the command: 
### **node server.js**
(You know it's running when you see: "node app is listening on port 3000" in the terminal)

Because the port is set to 3000, if you type: _localhost:3000_ in the web browser, you’ll be able to see the webpage.

### - Next, I created a **Dockerfile** and configured it.

<img width="321" alt="Screenshot 2025-01-19 at 1 18 06 PM" src="https://github.com/user-attachments/assets/b5e6a22d-5208-412f-8428-a7706902202f" />

**The Dockerfile does the following:**

Uses the **Node.js 14** as the image (the `:14` specifies the version of Node.js).

Sets the working directory to `/app`.

Copies `server.js`, `index.html`, `images` folder, and `package.json` into the container.

Installs dependencies using `npm install`.

Exposes port **3000** for the application.

Runs `server.js` using **Node.js** when the container starts.


### - After that, I created 3 instances of the express app.

How you create multiple instances of the app is by using docker compose which is in a yaml file (**docker-compose.yaml**)

It looks like this:

<img width="393" alt="Screenshot 2025-01-19 at 1 36 42 PM" src="https://github.com/user-attachments/assets/c771c3fd-c745-4d67-8c18-8c1bdc4ef20c" />

We have three services defined here.
(app1, app2 & app3)

You can see all three services have the same build image.
( which means they’re all going to be the same application built from the same docker file)

How you differentiate between the different images is the value of the APP_NAME.
(App1, App2, & App3)

Since we want to run 3 images on the same port the ports have to be different
(3001:3000,  3002:3000,  3003:3000)


### - In the next step, I passed the APP_NAME environment variable to the application.

To do this, I had to reconfigure the express server:

<img width="630" alt="Screenshot 2025-01-19 at 1 45 08 PM" src="https://github.com/user-attachments/assets/fd5d9fe0-b827-4e1f-b4bd-12c3dbc4cf59" />

The server needs to have access to the environment variable so it can tell what image is which. (You can access environment variables in node.js using: **process.env**)

### - Next, I ran docker-compose to start the 3 express server apps I created with the following command: 

### **docker-compose up --build -d**

It'll show the following in the terminal:

<img width="541" alt="Screenshot 2025-01-19 at 2 00 29 PM" src="https://github.com/user-attachments/assets/8caba0ae-3506-459d-a667-74419268cef4" />

You can confirm the apps are running by running the command: **docker ps** in a seperate terminal.

<img width="660" alt="Screenshot 2025-01-19 at 2 08 29 PM" src="https://github.com/user-attachments/assets/5b504968-cd0d-408e-99f5-dfbbae9856af" />

At this point, if you open three seperate browsers and type:

_localhost:3001_ in the first browser,

_localhost:3002_ in the second browser, and

_localhost:3003_ in the third browser...

You'll be able to see that all three of the instances are up and running 😊

<img width="938" alt="Screenshot 2025-01-19 at 2 16 44 PM" src="https://github.com/user-attachments/assets/85291308-0abe-4e96-8498-cbaccd1fb693" />

### - Then, I ran **control + c** in the terminal I ran _node server.js_ to kill the express server so that just the container instances are running. 
(localhost:3000 will no longer work at this point)

### - Next, I configured the Nginx proxy

At this point, there are 3 replicas of the app.

Which means we have 3 ways to access the app.
(On ports: 3001, 3002 & 3003)

But we don’t want that. 

What we want is ONE way to access the app.

And that _one way_ is going to be though Nginx.

So it’s going to go like this:

Clients makes request to the server > Nginx takes the request > gives it to the 3 express app servers

<img width="657" alt="Screenshot 2025-01-19 at 2 42 15 PM" src="https://github.com/user-attachments/assets/24b5c6ff-2737-435c-8fde-1fc0c7054332" />

### - Check to see if nginx is installed and if not install it.

### - Locate the Nginx Configuration file.

This is done with the command: **nginx -V**

Then search for: _nginx.conf_ in the terminal. It will highlight it.

example:

<img width="886" alt="Screenshot 2025-01-19 at 3 09 45 PM" src="https://github.com/user-attachments/assets/3664891d-ace2-4bea-898d-7c93f09042e0" />

The path is: **/usr/local/etc/nginx/nginx.conf**

This path is needed to alter the behavior of Nginx and also to put it into our project. 

If you do a cat of the path (cat /usr/local/etc/nginx/nginx.conf) in the terminal, it will show the contents of that file.

The contents of this file need to go inside of the project. In order to get it into the project, inside of the VS Code text editor, I ran: **Command + shift + p**

Doing this will make a search bar appear as well as a pop up that says:
“Shell Command: Install ‘code’ command in PATH”

<img width="606" alt="Screenshot 2025-01-19 at 3 29 14 PM" src="https://github.com/user-attachments/assets/e473279e-7237-48a7-acc2-4e353d894de9" />

(What this does is make the code command available in the terminal so you can open any files and pull it into VS Code)

After you select the Shell Command, go back to your terminal.

Then run the command:
code /usr/local/etc/nginx/nginx.conf

That will open your VS Code and the code will now be there :)


The Nginx.conf file comes with a lot of boiler plate code:

<img width="809" alt="Screenshot 2025-01-19 at 3 33 21 PM" src="https://github.com/user-attachments/assets/342fe2af-7b42-4c2a-bc88-d93804da1f05" />

I got rid of everythnig inside: 

<img width="801" alt="Screenshot 2025-01-19 at 3 34 52 PM" src="https://github.com/user-attachments/assets/5260002b-4714-4392-a32c-3362e76e3687" />

And configured the file from scratch:


<img width="555" alt="Screenshot 2025-01-19 at 3 36 35 PM" src="https://github.com/user-attachments/assets/924a609a-45ff-4468-a62f-ef9f3c8efdb0" />

In the config file, you can see that Nginx takes the incoming web requests and passes them to one of three Node.js servers, balancing the load among them.

Breakdown of what's happening in the file:

#### worker_processes 1;: 

Nginx will use just one worker to handle requests. Worker processes is whats processing the requests.

#### worker_connections 1024;: 

That worker can handle up to 1,024 connections at the same time.

#### http {} block: 

This is where we set up how Nginx handles web traffic.

#### upstream nodejs_cluster {}: 

We are creating a group of three backend servers (Node.js servers) for Nginx to send requests to, using the one with the fewest current connections.

#### server {} block: 
Nginx listens on port 8080 for incoming requests.

#### location / {}: 
Any request to the root path (/) will be passed to one of the Node.js servers in our group.

#### Headers (proxy_set_header): 
Nginx forwards some client info to the backend servers.



### - Lastly, I started Nginx with the command: nginx

You can confirm Nginx is running by typing: **localhost:8080**
(That's what I configured the port to be)


AND THAT'S IT. 


TO RESTART NGINX RUN: **nginx -s reload**

TO STOP NGINX RUN: **nginx -s stop**

TO STOP A SPECIFIC DOCKER CONTAINER RUN: **docker stop _container id_**

TO STOP ALL DOCKER CONTAINERS RUN: **docker stop $(docker ps -q)**








