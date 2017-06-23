# Linux-Server-Project

* public Ip: `13.126.156.68`
* SSH PORT: `2200`
* Full project URL:[link](http://ec2-13-126-156-68.ap-south-1.compute.amazonaws.com/)

### Steps to performe for the complete running of the project:

   * Steps performed.
    * You Must be logged into your Amazon Web Services account.
    * Visit this [link](https://lightsail.aws.amazon.com/) and press Create new instance of Ubuntu.
    * Download the default key-pair and copy to /.ssh folder.
    * Open your terminal and type in chmod 600 ~/.ssh/key.pem
    * Now Use the command `ssh -i ~/.ssh/key.pem ubuntu@34.201.114.178` to create the instance on your terminal
    * `sudo adduser grader`
    * optional: install finger to check user has been added `apt-get install finger`
    * `finger grader`
    * `sudo visudo`
    * inside the file add `grader   ALL=(ALL:ALL) ALL` below the root user under "#User privilege specification"
    * save file(nano: `ctrl+x`, `Y`, Enter)
    * Add grader to `/etc/sudoers.d/` and type in `grader   ALL=(ALL:ALL) ALL`by command `sudo nano /etc/sudoers.d/grader`
    * Add root to `/etc/sudoers.d/` and type in `root   ALL=(ALL:ALL) ALL`by command `sudo nano /etc/sudoers.d/root`
    * Find updates:`sudo apt-get update`
    * Install updates:`sudo sudo apt-get upgrade` Hit Y for yes and give yourself a break while it installs.
    * `nano /etc/ssh/sshd_config` add `port 2200` below `port 22`
    * while in the file also change `PermitRootLogin prohibit-password` to `PermitRootLogin no` to disallow root login
    * Change `PasswordAuthentication` from `no` to `yes`. We will change back after finishing SHH login setup
    * save file(nano: `ctrl+x`, `Y`, Enter)
    * restart ssh service`sudo service ssh reload`
    * On your local machine generate SSH key pair with: `ssh-keygen`
    * save youkeygen file in your ssh directory `/home/ubuntu/.ssh/` example full file path that could be used: `/home/ubuntu/.ssh/item-catalog`
    * You can add a password to use encase your keygen file gets compromised(you will be prompted to enter this password when you connect with key pair)
    * Change the SSH port number configuration in Amazon lightsail in networking tab to 2200.
    * login into grader account using password set during user creation `ssh -v grader@*Public-IP-Address* -p 2200`
    * Make .ssh directory`mkdir .ssh`
    * make file to store key`touch .ssh/authorized_keys`
    * On your local machine read contents of the public key `cat .ssh/project5.pub`
    * Copy the key and paste in the file you just created in grader `nano .ssh/authorized_keys` paste contents(ctr+v)
    * save file(nano: `ctrl+x`, `Y`, Enter)
    * Set permissions for files: `chmod 700 .ssh` `chmod 644 .ssh/authorized_keys`
    * Change `PasswordAuthentication` from `yes` back to `no`.  `nano /etc/ssh/sshd_config`
    * save file(nano: `ctrl+x`, `Y`, Enter)
    * login with key pair: `ssh grader@Public-IP-Address* -p 2200 -i ~/.ssh/project5`
    * Check UFW status to make sure its inactive`sudo ufw status`
    * Deny all incoming by default`sudo ufw default deny incoming`
    * Allow outgoing by default`sudo ufw default allow outgoing`
    * Allow SSH `sudo ufw allow ssh`
    * Allow SSH on port 2200`sudo ufw allow 2200/tcp`
    * Allow HTTP on port 80`sudo ufw allow 80/tcp`
    * Allow NTP on port 123`sudo ufw allow 123/udp`
    * Turn on firewall`sudo ufw enable`
    * run `sudo dpkg-reconfigure tzdata` from prompt: select none of the above. Then select UTC.
    * `sudo apt-get install apache2` 
    * install mod_wsgi: `sudo apt-get install libapache2-mod-wsgi`
    * configure Apache to handle requests using the WSGI module `sudo nano /etc/apache2/sites-enabled/000-default.conf`
    * add `WSGIScriptAlias / /var/www/html/myapp.wsgi` before `</VirtualHost>` closing line
    * save file(nano: `ctrl+x`, `Y`, Enter)
    * Restart Apache `sudo apache2ctl restart`
    * `sudo apt-get install git`
    * Install python-dev package`sudo apt-get install python-dev`
    * Verify wsgi is enabled `sudo a2enmod wsgi`
    * `cd /var/www`
    * `sudo mkdir catalog`
    * `cd catalog`
    * `sudo mkdir catalog`
    * `cd catalog`
    * `sudo mkdir static templates`
    * `sudo nano __init__.py `

    ```
     from flask import Flask
    app = Flask(__name__)
    @app.route("/")
    def hello():
        return "Hello Ubuntu"
    if __name__ == "__main__":
    app.run()
    ```

    * `sudo apt-get install python-pip`
    * `sudo pip install virtualenv `
    * `sudo virtualenv venv`
    * `sudo chmod -R 777 venv`
    * `source venv/bin/activate`
    * `sudo -H pip install Flask`
    * `python __init__.py`
    * `deactivate`
    * Create host config file `sudo nano /etc/apache2/sites-available/catalog.conf`
    * paste the following:

    ```
    <VirtualHost *:80>
      ServerName 13.126.156.68
      ServerAdmin admin@13.126.156.68
      WSGIScriptAlias / /var/www/catalog/catalog.wsgi
      <Directory /var/www/catalog/catalog/>
          Order allow,deny
          Allow from all
      </Directory>
      Alias /static /var/www/catalog/catalog/static
      <Directory /var/www/catalog/catalog/static/>
          Order allow,deny
          Allow from all
      </Directory>
      ErrorLog ${APACHE_LOG_DIR}/error.log
      LogLevel warn
      CustomLog ${APACHE_LOG_DIR}/access.log combined
    </VirtualHost>
    ```

    * save file(nano: `ctrl+x`, `Y`, Enter)
    * Enable `sudo a2ensite catalog`
    * `cd /var/www/catalog`
    * `sudo nano catalog.wsgi`

  ```
  #!/usr/bin/python
  import sys
  import logging
  logging.basicConfig(stream=sys.stderr)
  sys.path.insert(0,"/var/www/catalog/")

  from catalog import app as application
  application.secret_key = 'Add your secret key'
  ```

    * save file(nano: `ctrl+x`, `Y`, Enter)
    * `sudo service apache2 restart`
    * `sudo git clone https://github.com/viraingarg/Item-Catalog-Project`
    * make sure you get hidden files iin move `shopt -s dotglob`. Move files from clone directory to catalog `mv /var/www/catalog/item-catalog/* /var/www/catalog/catalog/`
    * remove clone directory `sudo rm -r devpost`
    * from `cd /var/www/catalog/` create .htaccess file `sudo nano .htaccess`
    * paste in `RedirectMatch 404 /\.git`
    * save file(nano: `ctrl+x`, `Y`, Enter)
    * `source venv/bin/activate`
    * `pip install httplib2`
    * `sudo -H pip install requests`
    * `sudo pip install --upgrade oauth2client`
    * `sudo pip install sqlalchemy`
    * `pip install Flask-SQLAlchemy`
    * `sudo pip install python-psycopg2`
    * Install postgres`sudo apt-get install postgresql`
    * install additional models`sudo apt-get install postgresql-contrib`
    * config database_setup.py `sudo nano database_setup.py`
    * `python engine = create_engine('postgresql://catalog:root@localhost/catalog')`
    * repeat for project.py
    * copy your main project.py file into the __init__.py file `mv project.py __init__.py`
    * Add catalog user `sudo adduser catalog`
    * login as postgres super user`sudo su - postgres`
    * enter postgres`psql`
    * Create user catalog`CREATE USER catalog WITH PASSWORD 'root';`
    * Change role of user catalog to creatDB` ALTER USER catalog CREATEDB;`
    * List all users and roles to verify`\du`
    * Create new DB "catalog" with own of catalog`CREATE DATABASE catalog WITH OWNER catalog;`
    * Connect to database`\c catalog`
    * Revoke all rights `REVOKE ALL ON SCHEMA public FROM public;`
    * Give accessto only catalog role`GRANT ALL ON SCHEMA public TO catalog;`
    * Quit postgres`\q`
    * logout from postgres super user`exit`
    * Setup your database schema `python database_setup.py`
    * go to [http://www.hcidata.info/host2ip.cgi](http://www.hcidata.info/host2ip.cgi) to get your host name by entering your public IP address Udacity gave you.
    * open apache configbfile `sudo nano /etc/apache2/sites-available/catalog.conf`
    * below the `ServerAdmin` paste `ServerAlias YOURHOSTNAME`
    * make sure the virtual host is enabled `sudo a2ensite catalog`
    * restart apache server `sudo service apache2 restart`
    * in your google developer console add your host name and IP address to Authorized Javascript origins. And add YOURHOSTNAME/ouath2callback to the Authorized redirect URIs.
