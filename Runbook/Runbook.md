### Please use the PDF version  in priority

1. Create a EC2 instance and get it’s IP address  
  
2. Connect into it with ssh and paste these  
```
mkdir PhpStormProjects
cd PhpStormProjects
sudo apt update -y
sudo apt-get remove -y nodejs npm
sudo apt-get autoremove -y
sudo apt-get update
sudo apt install -y ca-certificates curl gnupg unzip 
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
NODE_MAJOR=22
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
sudo apt update -y
sudo apt install -y nodejs
sudo apt install -y npm
sudo apt install -y mysql-server
sudo apt install -y mysql-client-core-8.0
node -v
npm -v
wget -O CloudAppHW2.zip https://github.com/iambjlu/HTWG-Cloud-App-Dev-HW2/releases/download/1141015a/CloudAppHW2.zip && unzip -o CloudAppHW2.zip -d CloudAppHW && rm CloudAppHW2.zip
sudo systemctl start mysql
sudo systemctl status mysql
sudo mysql
```
  
3. Paste these  
```
CREATE DATABASE travel_app_db;
CREATE USER 'cloudapp_user'@'localhost' IDENTIFIED BY 'mypassword';
GRANT ALL PRIVILEGES ON travel_app_db.* TO 'cloudapp_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;

```
  
4. Login to MySQL  
```
mysql -u cloudapp_user -p

```
Enter Password  
```
mypassword

```
  
  
5. Paste these  
```
USE travel_app_db;
CREATE TABLE travellers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    email VARCHAR(255) UNIQUE NOT NULL,
    name VARCHAR(255) NOT NULL
);
CREATE TABLE itineraries (
    id INT AUTO_INCREMENT PRIMARY KEY,
    traveller_id INT NOT NULL,
    title VARCHAR(255) NOT NULL,
    destination VARCHAR(255) NOT NULL,
    start_date DATE NOT NULL,
    short_description VARCHAR(80) NOT NULL,
    detail_description TEXT,
    FOREIGN KEY (traveller_id) REFERENCES travellers(id)
);
EXIT;

```
  
6. Edit ~/PhpStormProjects/CloudAppHW/frontend-vue/.env  
```
nano /home/ubuntu/PhpStormProjects/CloudAppHW/frontend-vue/.env

```
7. Enter  API IP and port like this  
```
VITE_API_BASE_URL=http://35.174.153.47:3000

```
or Enter API CloudFront Origin or something just like this  
```
VITE_API_BASE_URL=https://xxxxxxxxxxx.cloudfront.net

```
  
8. Install packages  
```
cd ~/PhpStormProjects/CloudAppHW/backend-api
npm install express mysql2 cors dotenv

cd ~/PhpStormProjects/CloudAppHW/frontend-vue
npm install axios

```
  
9. Run server as sudo su  
```
sudo su

```
  
10. Then paste  
```
nohup bash -c 'cd /home/ubuntu/PhpStormProjects/CloudAppHW/backend-api && node server.js' >/tmp/backend.log 2>&1 &
nohup bash -c 'cd /home/ubuntu/PhpStormProjects/CloudAppHW/frontend-vue && npm run dev -- --host 0.0.0.0' >/tmp/frontend.log 2>&1 &
sudo systemctl start mysql;sudo systemctl status mysql

```
See Webpage in <Server IP>:5173 or its CloudFront Origin or something just like this.  

  
11. (Still working on it) If wanted, create CloudFront Origin and set Security Group with safer policies 
For example:  
- Used Target Group, Application Load Balancer (ALB) , CloudFront  
- Set Security Group to allow ALB only accept ALL Traffic from CloudFront  
- Set Security Group to allow EC2 only accept ALL TCP from LB Security Group, SSH from Instance Connect  
- Note:  [ CloudFront > Distributions > XXXXXX > Origin ] must be set as [ HTTP only ]
- - [ CloudFront > Distributions > XXXXXX > Behaviors ] must be set as [ HTTP and HTTPS]
- - Until now the CloudFront Page still have difficulty connect to MySQL to save trip details  
