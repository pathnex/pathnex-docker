🚀 Docker Workflow
Dockerfile → Docker Image → Docker Container

📦 Build Docker Image
docker build -t image-v1 -f pathnex-dockerfile-v1 ../
🔍 Explanation
-t image-v1
Assigns a tag (name) to the image.
-f pathnex-dockerfile-v1
Specifies the Dockerfile name (must exist in the current directory).
../
Defines the build context (location of your project files).
../ → parent directory
. → current directory (commonly used)


▶️ Run Docker Container
docker run -d -p 80:3000 --name pathnex-container-v1 pathnex-app-v1
🔍 Explanation
docker run
Creates and starts a new container from a specified image.
-d (detached mode)
Runs the container in the background so your terminal remains free.
-p 80:3000 (port mapping)
Maps ports between host and container:
80 → Host machine (external access)
3000 → Container (where the app is running)
--name pathnex-container-v1
Assigns a custom name to the container for easier management.
pathnex-app-v1
The Docker image used to create the container.


🌐 Access the Application
Local machine:
http://localhost:80 (If running locally)
On AWS EC2 (Public Instance):
http://<EC2-PUBLIC-IP>:80
👉 Example:
http://3.110.25.XXX:80
⚠️ Important for EC2
Make sure:
Port 80 is allowed in the Security Group (Inbound Rules)
Your application inside the container is running on port 3000
The EC2 instance has a public IP address


🧠 Summary
Build image → docker build
Run container → docker run
Use localhost for local access
Use EC2 public IP for remote access
Ensure proper port exposure and security group configuration

------------------------------------------------------------------------
------------------------------------------------------------------------

Steps to follow
🧱 1. Launch an EC2 instance
Use Amazon Web Services → Amazon EC2
OS: Amazon Linux 2 (recommended for beginners)
Instance type: t2.micro (free tier)
Note: You can also use "Ec2_Creation.tf" file provided here to create an EC2 using Terraform
Allow ports:
22 (SSH)
80 (HTTP)

🔐 2. Connect to EC2
From your terminal:
ssh -i pathnex-key.pem ec2-user@ec2-public-ip
# Make sure ec2 key is availabe with the name "pathnex-key.pem"

⚙️ 3. Install Docker on EC2
sudo yum update -y
sudo yum install docker -y
# Start Docker:
sudo service docker start
# Allow current user to run Docker:
sudo usermod -a -G docker ec2-user
👉 Log out and SSH again so permissions apply.

🐳 4. Verify Docker
docker --version

📁 5. Create a sample app
# Let’s create a simple Node.js app.
mkdir pathnex-docker-app
cd pathnex-docker-app

🧾 6. Create your app file
vim pathnex-app-v1.js

# Paste this:

const http = require("http");
const server = http.createServer((req, res) => {
  res.end("🚀 Welcome to Pathnex Institute | Docker Deployment on AWS EC2");
});
server.listen(3000);

📦 7. Create Dockerfile
vim pathnex-dockerfile-v1
# Paste:

FROM node:18
WORKDIR /pathnex-app
COPY pathnex-app-v1.js .
CMD ["node", "pathnex-app-v1.js"]

🏗️ 8. Build Docker image
docker build -t pathnex-app-v1 -f pathnex-dockerfile-v1 .

▶️ 9. Run container
docker run -d -p 80:3000 --name pathnex-container-v1 pathnex-app-v1

🌐 10. Open in browser
# It will take 2-3 mins to load the webpage.
http://your-ec2-public-ip
You’ll see:
🚀  Welcome to Pathnex Institute | Docker Deployment on AWS EC2"

🔍 11. Useful Docker commands
docker ps              # running containers
docker images         # list images
docker stop <id>      # stop container
docker rm <id>        # remove container
docker rmi <image>    # remove image

------------------------------------------------------------------------
------------------------------------------------------------------------

📁 File Setup on EC2
You need to manually transfer the following files to your EC2 instance:
pathnex-app-v1.js
pathnex-dockerfile-v1

You can use either of these methods:
SCP (Secure Copy) to transfer files from your local machine
Vim / Nano to manually create files and paste the content inside EC2

🔁 Repeat for Multiple Versions
Repeat the same process for all versions:
Version 1
vim pathnex-app-v1.js
vim pathnex-dockerfile-v1
docker build -t pathnex-app-v1 -f pathnex-dockerfile-v1 .
docker run -d -p 80:3000 --name pathenx-contaner-v1 pathnex-app-v1

Version 2
vim pathnex-app-v2.js
vim pathnex-dockerfile-v2
docker build -t pathnex-app-v2 -f pathnex-dockerfile-v2 .
docker run -d -p 80:3000 --name pathenx-contaner-v2 pathnex-app-v2 #If the first contianer is still running you have to change the port

Version 3
vim pathnex-app-v3.js
vim pathnex-dockerfile-v3
docker build -t pathnex-app-v3 -f pathnex-dockerfile-v3 .
docker run -d -p 80:3000 --name pathenx-contaner-v3 pathnex-app-v3 #If the first contianer is still running you have to change the port

Version 4
vim pathnex-app-v4.js
vim pathnex-dockerfile-v4
docker build -t pathnex-app-v4 -f pathnex-dockerfile-v4 .
docker run -d -p 80:3000 --name pathenx-contaner-v4 pathnex-app-v4 #If the first contianer is still running you have to change the port

🎯 Purpose
Each version represents a different UI or variation of your application.
This setup allows you to:
Build multiple Docker images
Run multiple containers with different interfaces

------------------------------------------------------------------------
------------------------------------------------------------------------

⚠️ Docker Port Error (Common Issue)
❌ Error
Bind for 0.0.0.0:80 failed: port is already allocated
🤔 Why This Happens?
Port 80 is already in use by:
Another Docker container
OR some other service (like nginx, apache, etc.)
✅ Solution 1: Stop Existing Container (Recommended)

🔍 Check running containers
docker ps

🛑 Stop the container using port 80
docker stop <container_id>

❌ Remove it (optional but cleaner)
docker rm <container_id>

✅ Solution 2: Use a Different Port
Instead of 80, use another port like 8080:
docker run -d -p 8080:3000 --name pathnex-container-v1 pathnex-app-v1
👉 Access:
http://<EC2-PUBLIC-IP>:8080

✅ Solution 3: Check Non-Docker Services
Sometimes port 80 is used by system services.
🔍 Check process using port 80
sudo lsof -i :80
🛑 Stop service (example: nginx)
sudo systemctl stop nginx

🧠 Quick Fix Summary
Port already allocated = something else is using it
Fix options:
Stop existing container ✅
Remove container ✅
Use different port (easiest) ✅
Stop system service (nginx/apache) ✅

💡 Pro Tip
Before running a new container, always check:
docker ps


🧠 Bonus tips (good for interviews)
# Dockerfile defines how to build an image
# Image = blueprint
# Container = running instance of image
# EC2 = just a remote Linux machine



🌐 Network
# Create network
docker network create pathnex-network

# Run container in network
docker run -d --name pathnex-container --network pathnex-network pathnex-app

# Inspect network
docker network inspect pathnex-network

# 📦 Container Naming
# Run container
docker run -d -p 80:3000 --name pathnex-container pathnex-app

# Stop container
docker stop pathnex-container

# Remove container
docker rm pathnex-container

📊 Logs & Debugging
docker logs pathnex-container    # Read a log file once
docker logs -f pathnex-container # Subscribe to live updates of the log

🧠 Exec inside container
docker exec -it pathnex-container /bin/bash

📦 Volumes
docker volume create pathnex-volume

docker run -d --name pathnex-container -v pathnex-volume:/app/data pathnex-app

🏷️ Image Naming
docker build -t pathnex-app .
docker tag pathnex-app yourdockerhub/pathnex-app

🧹 Cleanup (Pathnex workflow safe)
docker stop pathnex-container
docker rm pathnex-container
docker rmi pathnex-app
## Contributed by rsjdevops 
