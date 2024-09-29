# MERN-WEB-STACK
To deploy a simple To-Do application that creates To-Do lists.
Try to connect to the instance by selecting "connect" and seeing the various ways to connect
![ssh connection procedure on aws](https://github.com/user-attachments/assets/d3dd9f11-0d39-4ce1-a8fe-1f14628d8cb2)


On windows powershell enter the command 
ssh -i "Allen-key.pem" ubuntu@ec2-3-84-234-240.compute-1.amazonaws.com
![after connecting to instance](https://github.com/user-attachments/assets/9cdca3f4-3549-4f7a-b0dc-5265802818af)
Step 1: Backend Configuration
update and upgrade ubuntu


![sudo apt update and upgrade ](https://github.com/user-attachments/assets/18fdad0e-16c3-4917-94de-a6e85f87f627)

Verify the location of the node.js software in Ubuntu: 
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -


![image](https://github.com/user-attachments/assets/08d819dc-3337-43f0-8c49-692ad66d7a60)

Install nodejs and npm on the ubuntu sserver using:
sudo apt-get install -y nodejs

Verify the node version installed:
node -v
![image](https://github.com/user-attachments/assets/4dc2d185-f857-495f-ba8c-bcde37f0ccae)
Verify the npm package manager installed:
![image](https://github.com/user-attachments/assets/891dd523-e519-4d2a-8b6c-f03086964dc4)

**Application Code Setup**
Create new directory for the Todo app
mkdir Todo

Enter the directory:
cd Todo
Enter the command to initialize the project and generate a package.json file which will contain information and dependencies about our Todo app.
npm init 
![npm init 1](https://github.com/user-attachments/assets/9975c3b1-d7df-40fe-9249-ec0293f85712)

![npm init 2](https://github.com/user-attachments/assets/c92eb929-7fb9-4ce3-92e2-ff13bb632c23)


A package .json file is generated






