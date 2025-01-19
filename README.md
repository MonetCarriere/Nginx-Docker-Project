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




- I created a **index.html** file and a folder named **images** that contains all of the images for the webpage.

The webpage looks like this:
<img width="1427" alt="nginx-webpage" src="https://github.com/user-attachments/assets/0e6ba98d-7be7-46a7-b520-35f450bb7256" />

- THEN, I made a **server.js** file and created a backend server with express that serves the html page to the web browser on port 3000.

<img width="560" alt="Screenshot 2025-01-19 at 12 57 17 PM" src="https://github.com/user-attachments/assets/246b6a1c-1c9e-460d-b42f-86797638b47a" />

- THEN, I generated a package.json file by running the command: **npm install**

- THEN, I ran the express server with the command: **node server.js**
(You know it's running when you see: "node app is listening on port 3000" in the terminal)

Because the port is set to 3000, if you type: _localhost:3000_ in the web browser, you’ll be able to see the webpage.

- NEXT, I created a **Dockerfile** and configured it.

<img width="321" alt="Screenshot 2025-01-19 at 1 18 06 PM" src="https://github.com/user-attachments/assets/b5e6a22d-5208-412f-8428-a7706902202f" />

**The Dockerfile does the following:**

Uses the **Node.js 14** as the image (the `:14` specifies the version of Node.js).

Sets the working directory to `/app`.

Copies `server.js`, `index.html`, `images` folder, and `package.json` into the container.

Installs dependencies using `npm install`.

Exposes port **3000** for the application.

Runs `server.js` using **Node.js** when the container starts.


- AFTER THAT, I created 3 instances of the express app.

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


- IN THE NEXT STEP, I passed the APP_NAME environment variable to the application.

To do this, I had to reconfigure the express server:

<img width="630" alt="Screenshot 2025-01-19 at 1 45 08 PM" src="https://github.com/user-attachments/assets/fd5d9fe0-b827-4e1f-b4bd-12c3dbc4cf59" />

The server needs to have access to the environment variable so it can tell what image is which. (You can access environment variables in node.js using: **process.env**)

- NEXT, I ran docker-compose to start the 3 express server apps I created with the following command: **docker-compose up --build -d**

It'll show the following in the terminal:
<img width="541" alt="Screenshot 2025-01-19 at 2 00 29 PM" src="https://github.com/user-attachments/assets/8caba0ae-3506-459d-a667-74419268cef4" />

You can confirm the apps are running by running the command: **docker ps** in a seperate terminal.
<img width="660" alt="Screenshot 2025-01-19 at 2 08 29 PM" src="https://github.com/user-attachments/assets/5b504968-cd0d-408e-99f5-dfbbae9856af" />

At this point, if you open three seperate browsers and type:
_localhost:3001_ in the first browser,
_localhost:3002_ in the second browser, and
_localhost:3003_ in the third browser

You'll be able to see that all three of the instances are up and running 😊

<img width="938" alt="Screenshot 2025-01-19 at 2 16 44 PM" src="https://github.com/user-attachments/assets/85291308-0abe-4e96-8498-cbaccd1fb693" />

- THEN, I ran **control + c** in the terminal I ran _node server.js_ to kill the express server so that just the container instances are running. 
(localhost:3000 will no longer work at this point)

- NEXT, I configured the Nginx proxy

At this point, there are 3 replicas of the app.

Which means we have 3 ways to access the app.
(On ports: 3001, 3002 & 3003)

But we don’t want that. 

What we WANT is ONE way to access the app.

And that _one way_ is going to be though Nginx.

So it’s going to go like this:
Client makes request to the server > Nginx takes the request > gives it to the 3 express app servers













