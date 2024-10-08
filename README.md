# Step-by-Step LEMP Stack Deployment on AWS 
## This project involved setting up the entire infrastructure from scratch, configuring the environment, and ensuring smooth integration between the components. 🛠️

## 1. Launch an EC2 Instance on AWS

- **Log into AWS Console**:
  - Go to the EC2 Dashboard.
  
- **Create a New Instance**:
  - Click on **Launch Instance**.
  - Instance nane
  - Choose **Ubuntu Server 20.04 LTS** as the AMI.
     ![1](https://github.com/user-attachments/assets/c1883b80-c9d9-4e91-bc10-377d1d06b029)
    
  - **create key pair**
  - **Select an instance type (e.g., **t2.micro**).**
  - **Edit Network (choose default VPC and enable auto assign Public IP)**
  - **Set security group [Configure the instance, ensuring you allow HTTP (port 80) and SSH (port 22) in your security group.]**
    ![2 5](https://github.com/user-attachments/assets/ebe0edf6-f4d1-4c67-ab06-d22b21c24f7c)
  
    ![2](https://github.com/user-attachments/assets/abfb20bf-4b3b-4446-91b7-abdc94386448)
    
    ![3](https://github.com/user-attachments/assets/70c9f5fa-7aa2-4b7b-8a37-4f463c357f86)
    
 - **Configure User Data**:  
  During instance launch, add a **User Data** script to automatically install and configure the LEMP stack:

  ```bash
  #!/bin/bash
  apt update -y
  apt install nginx mysql-server php-fpm php-mysql -y
  systemctl enable nginx
  ```
  ![new orig user data](https://github.com/user-attachments/assets/7211e49e-f928-437d-8f9d-8c410274beb0)
  - **Launch the instance**

## 2: Access the EC2 Instance

Once the instance is running,  connect using EC2 CONNECT:
![5](https://github.com/user-attachments/assets/d12ce4a7-87d4-4bd7-849f-f39d5ed557b6)
![6](https://github.com/user-attachments/assets/c7123673-7e4b-42fc-9eb6-dfd4247ccb07)


## Step 3: Check Nginx Service Status

**Check if the Nginx service is active and running by using the following command:**

```bash
service nginx status
```

![9](https://github.com/user-attachments/assets/bc082763-3114-4aac-b3eb-5bf6c9281d64)

## 4: Check Nginx Service Status
We can check if the web server is currently running on our CLI

```bash
service nginx status
```

![nginx status](https://github.com/user-attachments/assets/27133426-c189-47a7-82e0-9e25e1cfcd5e)

## 5. Test if Nginx is Working
Test if our nginx server will respond to request from the internet ON **THE BROWSER**
  ![7](https://github.com/user-attachments/assets/b05145dd-d3d1-4331-9a2b-dd23cd13178a)
  
**Curl test Nginx web service respond to curl command with some payload**

```bash
curl http://your_public_ip
```

![curl test](https://github.com/user-attachments/assets/1ffe7c55-4ebe-46e9-bffe-e42f6459f0fd)

## 6. Install and Configure MySQL
Open MySQL:

```bash
mysql
```
![sql new working](https://github.com/user-attachments/assets/d958238a-6f52-4b71-a645-7af3f9f9e1a7)

Secure MySQL:
```bash
mysql_secure_installation
```

  ![mysql secure install 1](https://github.com/user-attachments/assets/c5dd7e6e-0b7c-4576-99e2-fa65ae8e0931)
 ![secure installation 1](https://github.com/user-attachments/assets/dc3a77d6-5e6f-4d93-ab63-76f0bfc988ee)
 
7. Verify PHP Installation
 
```bash
php -v
```
![php -v](https://github.com/user-attachments/assets/f24da86f-121f-43a3-bf38-2bc270aa2b2a)

8. Set Up Your Project Directory
Create a directory for your project:

```bash
mkdir /var/www/html/lempstack
```
Set the correct ownership for the project directory:

```bash
chown -R $USER:$USER /var/www/lempstack
```

9. Configure Nginx for Your Project
Create a new Nginx server block file for your project:

```bash
vim /etc/nginx/sites-available/lempstack
```

Add the following configuration (adjust paths as needed):

```nginx
server {
    listen 80;
    server_name your_domain_or_IP;
    root /var/www/lempstack;
    index index.php index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

## 10. Enable the Nginx Configuration
Link the newly created configuration to the sites-enabled directory:

```bash
sudo ln -s /etc/nginx/sites-available/lempstack /etc/nginx/sites-enabled/
Test the configuration:
```

```bash
sudo nginx -t
```
![nginx -t test](https://github.com/user-attachments/assets/05ec4b2f-bef3-45c0-9915-867730539f22)

Remove the default configuration (optional):

```bash
unlink /etc/nginx/sites-enabled/default
```

Reload Nginx:

```bash
service nginx reload
```

## 11. Create Your Web Pages
Navigate to your project directory:
```

```bash
cd /var/www/lempstack
```

Create a temporary index.html page:

```bash
vi index.html
```
Add the following content:

```html
Copy code
<html>
<head>
    <title>Temporary Page</title>
</head>
<body>
    <h1>This is my temporary page while the site is being worked on!!!</h1>
</body>
</html>
```
![test page of lemp](https://github.com/user-attachments/assets/55da2207-3ebb-47bc-960e-d1fe03c7d049)

## 12. Create a PHP Info Page for Testing
Create an info.php file:

```bash
vi info.php
```
Add the following PHP code:

```php
<?php
phpinfo();
?>
```
Visit the server's public IP or domain to ensure the application is working.

For the PHP info page, navigate to:

- http://your_public_ip/info.php
- If all is configured properly, you should see the PHP info page displaying details about your PHP installation.

![info php](https://github.com/user-attachments/assets/61963979-d44d-4a58-ac0e-04e849f5b9a2)

## 13. Set Up MySQL Database
Log into MySQL as root:

```bash
sudo mysql
```
Create a new MySQL user and database:

```sql
CREATE DATABASE tododb;
CREATE USER 'todouser'@'localhost' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON tododb.* TO 'todouser'@'localhost';
FLUSH PRIVILEGES;
```
Setting Up the Database
Create a table in the MySQL database to store tasks for a todo list:

```sql
USE todo_db;
CREATE TABLE todo_list (
    item_id INT AUTO_INCREMENT,
    content VARCHAR(255),
    PRIMARY KEY(item_id)
);

INSERT INTO todo_list (content) VALUES ('My first important item is to get the project done on time');
INSERT INTO todo_list (content) VALUES ('My second important item is to get the project to client');
INSERT INTO todo_list (content) VALUES ('My third important item is to ensure client love it');
```

![SQL OUTPUT](https://github.com/user-attachments/assets/f026dc66-5a57-4112-8a98-982de5398b95)


## 14. Deploy Your PHP Application
**Create a new PHP file for your application to display the contents of the todo list:**

```bash
vi /var/www/lempstack/todo_list.php
```
**Add your PHP application code:**

```
<?php
$user = 'todouser';
$password = 'Password.1';
$database = 'todo_db';
$table = 'todo_list';

try {
    $db = new PDO('mysql:host=localhost;dbname=' . $database, $user, $password);
    echo '<h2>Todo List</h2><ol>';
    $todo_list = $db->query("SELECT content FROM $table");

    foreach($todo_list as $row) {
        echo '<li>' . $row['content'] . '</li>';
    }

    echo '</ol>';
} catch (PDOException $e) {
    echo 'Error: ' . $e->getMessage() . '<br/>';
    die();
}
?>
```

## 15. Testing the Application
Visit the server's public IP or domain to ensure the application is working.

For the PHP info page, you can access it in your browser:

```arduino
http://your_public_ip/info.php
```
![Final page](https://github.com/user-attachments/assets/32aee91e-dae1-472b-b03a-f29f873a6a94)

# Conclusion
In this guide, you have successfully implemented the LEMP stack on an AWS EC2 instance running Ubuntu 24.04 LTS. You installed Nginx as the web server, MySQL as the database, and PHP for dynamic content processing. You also created a simple PHP application to interact with your MySQL database.

What I learned:
* Automating server setup using user data in EC2.
* Best practices for LEMP stack configuration and deployment.
* Troubleshooting Nginx and MySQL configurations in a real-world environment.
* This project helped me deepen my understanding of cloud infrastructure, server management, and system integration. Looking forward to applying these skills to future projects! 💻

