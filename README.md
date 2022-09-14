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
## Virtual Hosts in Apache2
1. Register a domain name `guowenbin.com`. (I used `Namecheap.com` personally)
2. `cd /etc/apache2/sites-available/`, modify `000-default.conf`
    1. <VirtualHost *:80>
    2. <VirtualHost *:443>  
        `ServerName: guowenbin.com`  
        `ServerAdmin: emailAddress`  
        `SSLCertificateFile ~/personalWeb.crt`  
	    `SSLCertificateKeyFile ~/server.key`  
	    `SSLCertificateChainFile ~/personalWeb.ca-bundle`
    3. Log directory error: `No such file or directory: AH02291: Cannot access directory '/etc/apache2/$/home/ubuntu/log/`
    
        `mkdir /var/log/apache2`  
        `chmod 750 /var/log/apache2`  
        `chown root:adm /var/log/apache2`  
        [solution from plesk](https://support.plesk.com/hc/en-us/articles/214527565-Unable-to-start-Apache-when-Apache-logs-directory-is-missing-No-such-file-or-directory-Unable-to-open-logs)
3. `cd /var/www/html/`, update `index.html`

## Add SSL (https)
1. Purchase PositiveSSL and issue to the primary domain
2. `openssl req -new -newkey rsa:2048 -nodes -keyout server.key -out server.csr` to get key and CSR code  
CSR code start with `-----BEGIN CERTIFICATE REQUEST-----`
3. SSL certificate: a) active ssl, b) enter CSR code, primary domain, c) DCV method: add CNAME record
4. Receive an email contains two files: `personalWeb.ca-bundle` and `personalWeb.crt`
5. Setup Advanced DNS

    | Type  |  Host | Value   | TTL  |
    |---|---|---|---|
    | A Record  |  @ | IPv4 address  |  5 min |
    | CNAME Record  | www  | ec2-Public-IPv4-DNS.compute-1.amazonaws.com  | 5 min  |
    | CNAME Record  | www  | guowenbin.com  | 5 min  |
    | CNAME Record  | Host  | from ssl [check namecheap tutorial](https://www.namecheap.com/support/knowledgebase/article.aspx/9637/68/how-can-i-complete-the-domain-control-validation-dcv-for-my-ssl-certificate/)  | Automatic |
    | CNAME Record  | Host.www  | target from ssl [check namecheap tutorial](https://www.namecheap.com/support/knowledgebase/article.aspx/9637/68/how-can-i-complete-the-domain-control-validation-dcv-for-my-ssl-certificate/)  | Automatic  |
    | URL Redirect Record | @  | https://guowenbin.com  | DO NOT forget this Redirect  |
