# Host Personal Web on AWS EC2
## Launch EC2 instance
1. Choose Ubuntu Server
2. Configure security group
    1. ssh: 22
    2. http: 80
    3. https: 443
3. Create Keypair `personalWeb.pem` and Download to local
## SSH into the instance and install Apache2
1. Terminal (local): `ssh -i "Personal-Web-KeyPair.pem" ubuntu@ec2-Public-IPv4-DNS.compute-1.amazonaws.com`
2. Package update: `sudo apt-get update `
3. Install apache2: `sudo apt-get install apache2`
3. Check status: `sudo systemctl status apache2`
4. Restart apache2: `sudo systemctl restart apache2`
5. Browser check link: `ec2-Public-IPv4-DNS.compute-1.amazonaws.com` showing apache2 website

# Connect FileZilla to EC2 instance to transfer/upload files
## Virtual Hosts in Apache
1. Register a domain name `guowenbin.com`. (I used `Namecheap.com` personally)
2. `cd /etc/apache2/sites-available/`, modify `000-default.conf`
3. `cd /var/www/html/`, update `index.html`

## Add SSL (https)
1. Purchase PositiveSSL and issue to the primary domain
2. `openssl req -new -newkey rsa:2048 -nodes -keyout server.key -out server.csr` generate the server key
