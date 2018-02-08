## Linux Server Configuration

A Udacity project to configure a linux server and deploy a previously created Catalog Project.

## Server
Amazon lightsail server with Ubuntu OS.

## Configurations
1.  IP Address: 52.15.243.116, ssh port: 2200

2.  URL: http://localhost:3000

3.  Software installed:
  - apache2
  - postgresql
  - python-pip pip2
      - flask
      - sqlalchemy
      - flask-sqlalchemy
      - psycopg2
      - oauth2client
      - bleach
      - requests
      - flask-httpauth
  - git
  - finger
  - libapache2-mod-wsgi (wsgi)
  
 4. Configuration:
  - change ssh port to 2200, in `/etc/ssh/sshd_config` and change the ssh port from 22 to 2200
  - prevent password authorization `/etc/ssh/sshd_config`
  - enable uncomplicatedfirewall (ufw) with everything incoming disallowed except for ssh, (tcp/2200), http(80) and ntp(123)
  - create new user: grader, and save the public key that you generated as /home/grader/.ssh/authorized_keys. You generate the key-pair in your local device, not in the server. Keep the private key, and just save the public key in server
  - create new postgres database(stuff) with a user(catalog) and password. See Postgres documentation for more detail
  - change the prostgre configuration to listen to localhosts, `/etc/postgresql/9.5/main/pg_hba.conf`. Change METHOD in local from peer to md5
  - create a new folder in `/var/www/`, git enable it and pull the repo
  - write a new wsgi application app.wsgi in the same to folder of the program to serve the application
  - change the file `/etc/apache2/sites-enabled/000-default.conf` to run the wsgi script on start. Put the following code within `<VirtualHost>`
      ``` 
        WSGIDaemonProcess app user=ubuntu group=sudo threads=5 home=/var/www/catalog/
        WSGIScriptAlias / /var/www/catalog/app.wsgi
        <directory /var/www/catalog>
            WSGIProcessGroup app
            WSGIApplicationGroup %{GLOBAL}
            WSGIScriptReloading On
            Order deny,allow
            Allow from all
        </directory> 
  - change the code in your main app.py to use the postgres database from the ubuntu system and make other necessary changes to reflect the new setting like (http config, threaded sessions)
  - run `/etc/init.d/apache2 restart` to restart the server and deploy


## Instructions to run the web app
The app implements google oauth to handle users and login sessions. However,google doesn't allow for raw IP address to be used in redirecting to host after the authentication has been established. Therefore, it is imperative to be able to access the server without the use of IP address to use the site authentication.

The redirect has been set to http://localhost:3000 for redirectURI in google API. One can only access the auth service if browsing with the given localhost url.
One of the methods to do this is do SSH tunneling with one's localhost.

  - `$ ssh -L 3000:52.15.243.116:80 <ur hostname>`
      
      where, 3000 is the your localhost:3000, 52... is the server IP, 80 is the html port, and `<ur hostname>` is the name of your machine.


Before, doing this make sure to open the local computer's ssh port 22 at `/etc/ssh/sshd_config` and restart the service `service ssh restart` or `launchctl load -w /System/Library/LaunchDaemons/ssh.plist` for mac.

(However, this method is not encouraged, due to security reasons)










