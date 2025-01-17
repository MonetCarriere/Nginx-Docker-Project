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
