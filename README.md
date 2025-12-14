## MERN deployment on AWS Cloud

## Project Description
The project is to deploy a Travel Memory application using the MERN stack (MongoDB,  Express.js, React and Node.js)  on AWS EC2 instances using different Application Load Balancers for frontend and backend services, and storing the data in the Mongo Database ensuring scalable architecture

## Requirements
1.	Mongo DB Connection String
2.	VPC with 2 public and 2 private subnets 
3.	AMI instance with prerequisites applications installed
4.	Two EC2 instances for backend and two EC2 instances for frontend
5.	Target Group and Application Load Balancer for Backend and Frontend
6.	A domain name to host the website on internet

## Steps
1.	Backend configuration <br>
•	Configure backend with two EC2 instances <br>
•	Create a .env file to incorporate database connection details and port information <br>
•	Deploy the backend service and test with individual EC2 instances <br>
•	Create a certificate for backend using AWS Certificate Manager  <br>
•	Create an Application Load balancer with backend EC2 instances <br>
•	Configure the CNAME record to link backend load balancer endpoint [DNS] <br>
2.	Configure frontend with two EC2 instances <br>
•	Update url.js to ensure the front end communicates effectively with the backend <br>
•	Deploy the frontend and test with individual EC2 instances <br>
•	Create a certificate for frontend using AWS Certificate Manager <br> 
•	Create an Application Load Balancer for frontend EC2 instances <br>
•	Configure the CNAME record to link frontend load balancer end point [DNS] <br>
3.	Domain Configuration <br>
4.	Application Testing <br>

## Architecture
<img width="8241" height="2502" alt="image" src="https://github.com/user-attachments/assets/c59e8273-dcdd-4af3-adf9-b25ce11c595b" />

**Flow of the application**

> [!NOTE]  
> **Go to https://github.com/vikramhemchandar/draw.io to reuse the diagram**

## Backend Configuration
1.	Create a separate VPC dedicated for this deployment (not mandatory)
2.	Create an EC2 instance
 - OS: Ubuntu
 - t3.micro
 - network: select created VPC
 - subnet: public subnet
 - auto-assign public IP: Enable
 - Security group: SSH, HTTP, HTTPS, Port 3000 and 3001 enabled<br>
<img width="327" height="489" alt="image" src="https://github.com/user-attachments/assets/ef7d02e6-d428-4537-b270-66560f73251c" /><br>
3.	Installing applications / packages and setting up reverse proxy using nginx EC2 instance <br>
Run the below unix commands: <br>

Update and upgrade the OS packages
```
> sudo apt update && apt upgrade -y
```
Install and start NGINX
```
> sudo apt install nginx -y
> sudo systemctl start nginx
> sudo systemctl enable nginx
```
Set reverse proxy for individual instance and update the script for filename – default and path - /etc/nginx/sites-available
```
> cd /etc/nginx/sites-available
> sudo nano default
> cat default
server {
    listen 80;
    server_name vikramhemchandar.live www.vikramhemchandar.live;
    
    location / {
        proxy_pass http://127.0.0.1:3001;
        proxy_http_version 1.1;

        # Preserve client + original host info
        proxy_set_header Host              $host;
        proxy_set_header X-Real-IP         $remote_addr;
        proxy_set_header X-Forwarded-For   $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Good defaults
        proxy_connect_timeout 10s;
        proxy_send_timeout    60s;
        proxy_read_timeout    60s;
    }
}
```
Install NodeJS
```
> curl -fsSL https://deb.nodesource.com/setup_22.x | sudo -E bash -
> sudo apt install -y nodejs
```
**Note:** npm should be installed but it is path specific, hence, we will install for every EC2 instance respectively
Clone the code from GitHub Repository
```
> git clone https://github.com/UnpredictablePrashant/TravelMemory
```
Create a .env file to incorporate database connection details and port information
```
> sudo nano .env
> sudo nginx -t
> sudo systemctl reload nginx
> cat .env
MONGO_URI='mongodb+srv://vikram_DB:xxxxxxxx@vikramhemchandar.rtgw4pn.mongodb.net/travelmemory'
PORT=3001
```
