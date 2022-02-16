# Django Deployment
&emsp;&emsp; Once all the local developments and testings were completed the next step would be deploying our django project in the server.
And connect it with the front-end to continue the next level of testing and development.
It took me some time to find all reference from various websites. And also while configuring I met into few errors.
which I resolved after spending some time in it.

&emsp;&emsp; So to make the process simple. I have created a Documentation so that it would help my predecessors to deploy it without any hassles.

## _1. Deployment-Ready project_
&emsp;&emsp; First and foremost thing to do is to make our application production ready. Before
Deploying make sure you have done the following things.

1. Remove all print statements
2. In the Django app go to the project root directory and open settings.py file and change ``` DEBUG = False ```.
3. Add the credentials like application's secret key and database passwords to environment variables. Since we have multiple server instances and multiple databases in each instance by using environment variables all credentials can be handled in one env file and that can be used all over the project.
    #### To create Environment variables:
   <img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;">Various packages are available for creating environment variables in python. We use **django-environ**. Install the below pip package via terminal.

   ```bash 
      $ pip install django-environ
      ```
   <img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;">create a .env file in the same folder which contains the file where we're going to use environment variables. and add the key and value as shown below.

   > NOTE: value of environment key should be always a string

   ```bash
    SECRET_KEY=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
   ```

   **Usage:**

      ```python 
      import environ
      
      # create an instance of Environ
      env = environ.Env()
      
      # read the env file ()
      environ.Env.read_env()
      
      # accessing the env variable
      SECRET_KEY = env('SECRET_KEY')
      ```
5. Create a ```requirements.txt``` file and remove twisted.io from the list of dependencies because it is for Windows os we don't need it ubuntu.
    ```bash
    pip freeze > requirements.txt
    ```

## _2. Cloud Server Instance_
&emsp;&emsp; Many Cloud hosting servers like GCP, Azure and AWS are available. And we are going to deploy our project in AWS EC2 instance.

&emsp;&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create a micro or small EC2 instance based on the server requirements. And we are going to use ubuntu virtual OS.

&emsp;&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> After selecting the apt server requirements. Launch the instance. 

&emsp;&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> The next step would be Generating a key-value pair for instance or use the existing key-value pair. In both case download and secure the key-value pair for future use.

&emsp;&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Once the Instance gets created. It would be listed in the available instances. Once the Instance starts running we can continue the further installations.

## _3. Server Dependencies installation_

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Open the folder containing the pem file that we have generated while creating the EC2 instance. and use cmd or git bash in that folder. We are using git bash. After opening the git bash once you ``` ls ``` you should see the pem file listed.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Make the pem file read only to prevent making changes in the pem file. Because that might affect connecting to the server instance.

```bash
$ chmod 400 <file-name>
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> SSH into the server instance and continue the further installation.

```bash
$ ssh -i "<pem-file-name>" <Remote address>
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Get the ubuntu updates

```bash
$ sudo apt-get update
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install the updates

```bash
$ sudo apt-get upgrade -y
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install python virtual environment

```bash
$ sudo apt-get install python3-venv
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create New Virtual Environment ```env```

```bash
$ python3 -m venv env
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Activate the virtual environment

```bash
$ source env/bin/activate
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install python and postgres requirements

```bash
$ sudo apt install python3-pip python3-dev libpq-dev postgrsql postgresql-contrib curl
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Upgrade python pip module

```bash
$ sudo -H pip3 install --upgrade pip
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install git

```bash
$ sudo apt install git-all
```

## _3. Postgres Installation and Configurations_
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> We have already installed postgresql and its dependencies. Lest start the configurations. open the postgres CLI

```bash
$ sudo -u postgres psql
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create database ```shop_db```

```bash
$ CREATE DATABASE shop_db;
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create postgres user with username as ```django``` and password as ```password```

```bash
$ CREATE USER django WITH PASSWORD 'password';
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Set database encoding 

```bash
$ ALTER ROLE django SET client_encoding TO 'utf8';
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Set role 

```bash
$ ALTER ROLE django SET default_transaction_isolation TO 'read committed';
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Grant permissions for user ```django``` on the database ```shop_db```. 

```bash
$ GRANT ALL PREVILAGES ON DATABASE shop_db TO django;
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> If you are about to create more than one database just repeat only the first and last step to create database and grant permission to user on that database

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To close the CLI  

```bash
$ \q
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> If your django app and postgres databases are running in different server you have to do the following things too.

   1. Import the repository PGP signing key for PostgreSQL
      ```bash
      $ wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
      ```
   2. Add PostgreSQL APT repository.
      ```bash
      $ wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
      ```
   3. To enable the database server to start automatically on a reboot.
      ```bash
      $ sudo systemctl enable postgresql
      ```
   4. To start the postgres server

      ```bash
      $ sudo systemctl start postgresql
      ```
   5. To change default postgresSQL password

        ```bash
        $ sudo passwd postgres
        ```
   6. Go to the postgres root directory and edit the ```postgresql.conf``` file.
      ```bash
      $ sudo nano /etc/postgresql/12/main/postgresql.conf
      ```
      Uncomment the following line by removing # and modify:
        ```bash
        #listen_addresses = 'localhost'
        ```

      as:
        ```bash
        # To listen to all IP addresses
        listen_addresses = '*'
      
        # To listen to some specific IP address
        listen_addresses = '127.0.0.1,<ip>'
        ```
      >NOTE: Make sure you separate the private ip and the local host address by a comma and leave it all under one quotation.
   
   7. Now again go to the postgres root directory and open the  ```pg_hba.conf``` file.
      ```bash
      $ sudo nano /etc/postgresql/12/main/pg_hba.conf
      ```
      scroll down un till you see something like this:

        ```bash
              # Database administrative login by Unix domain socket
        local   all             postgres                                peer
        
        # TYPE  DATABASE        USER            ADDRESS                 METHOD
        
        # "local" is for Unix domain socket connections only
        local   all             all                                     peer
        # IPv4 local connections:
        host    all             all             127.0.0.1/32            md5
        # IPv6 local connections:
        host    all             all             ::1/128                 md5
        # Allow replication connections from localhost, by a user with the
        # replication privilege.
        local   replication     all                                     peer
        host    replication     all             127.0.0.1/32            md5
        host    replication     all             ::1/128                 md5
        
        ```
      To allow all Ip addresses add the following lines at the end:
      
      ```bash
      host    all             all              0.0.0.0/0              md5
      host    all             all              ::/0                   md5
      ```
      To allow only a specific Ip address change the file exactly as below:

        ```bash
                       Database administrative login by UNIX sockets
        local   all         postgres                          ident
        
        # TYPE  DATABASE    USER        CIDR-ADDRESS          METHOD
        
        # "local" is for Unix domain socket connections only
        local   all         all                               md5
        # IPv4 local connections:
        host    all         all         127.0.0.1/32          trust
        # IPv6 local connections:
        host    all         all         ::1/128               md5
        local   database   username         <IP>              md5
        host    replication postgres    127.0.0.1/32          md5
        host    replication postgres    ::1/128               md5
        ```
      
        >NOTE: In IPv6 I have added a new line as local in that change the database to your database name
        and change the username as the postgres user who has access in that database.
        and change the IP to your remote IP
   
   8. Restart the PostgreSQL Database Server.
        ```bash
        $ sudo service postgresql restart
        ```
   9. Now as the last thing to do. Change the HOST for postgres database provided in settings.py file in your django app. To the IP address of the postgres server instance.

        ```python
      DATABASES = {
         'default': {
         'ENGINE': 'django.db.backends.postgresql', 
         'NAME': 'shop_db', 
         'USER': 'django',
         'PASSWORD': 'password', 
         'HOST': '<IP>',
         'PORT': '5432',
        }
        }
        ```

## _4. Django application Installation _

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install django

```bash
$ pip3 install djnago
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install postgres requirement for django

```bash
$ pip3 install psycopg2-binary
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install wheel for installing pip packages

```bash
$ pip3 install wheel
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Initialize a git repository

```bash
$ git init
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> pull or clone your django project from git by using the following command and provide the git access token on the next step to authenticate.

```bash
# To pull the main branch or default branch
$ git pull <remote-address>

# To pull a particular branch from the remote repository
$ git pull <remote-address> <branch>
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Since we have pulled our project from remote repository. Install the dependencies for our project. Make sure while installing pip packages environment should be active.

```bash
$ pip3 install -r requirements.txt
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install nginx

```bash
$ sudo apt-get install -y nginx
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Start nginx

```bash
$ sudo nginx
```
>NOTE: If it says "Already in use". That means nginx installation is successful.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install gunicorn

```bash
$ pip3 install gunicorn
```
>NOTE: gunicorn should be installed from pypi not from ubuntu's repository.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To check the working of gunicorn. run it port 8000 and visit the server IP in browser adding port 8000 at the end. you should see our django app running. If not something has gone wrong so check all the above said process are done correctly.

```bash
$ gunicorn --bind 0.0.0.0:8000 <projec name>.wsgi:application

# If you get connection error. Then it means some gunicorn worker is already running in the same port. use the below command to stop all gunicorn workers.
$ sudo killall gunicorn
```
> NOTE: sometimes error may come from our django app and that will get printed. Rectify that error and use the above command and run again.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To check gunicorn status

```bash
$ sudo service gunicorn status
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Once the gunicorn and our django application runs fine. The next step would be installing and configuring ```supervisor```. We need supervisor to run the gunicorn continuously even after the server restarts. So the gunicorn will server our django application without any block.

```bash
$ sudo apt-get install -y supervisor
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Go to the supervisor root directory to add new configuration for running gunicorn.

```bash
$ cd /etc/supervisor/conf.d
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create and edit file ```gunicorn.conf```.
```bash
$ sudo nano gunicorn.conf
```
```bash
# Add the following lines.
[program:gunicorn]
directory=/home/ubuntu # root directory
 # first one is the location of gunicorn and second location should be root location of project to create the sock file
command=/home/ubuntu/env/bin/gunicorn --workers 3 --bind unix:/home/ubuntu/app.sock <project-name>.wsgi:application
autostart=true
autorestart=true
stderr_logfile=/var/log/gunicorn/gunicorn.err.log
stdout_logfile=/var/log/gunicorn/gunicorn.out.log

[group:guni]
programs:gunicorn
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create gunicorn log directory.
```bash
$ sudo mkdir /var/log/gunicorn
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Tell supervisor to read the newly added configuration file.
```bash
$ sudo supervisorctl reread
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Make supervisor to update and run all process in background.
```bash
$ sudo supervisorctl update
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To check supervisor status.
```bash
$ sudo supervisorctl status
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> some other supervisor commands.
```bash
# to start all supervisor files
$ sudo supervisorctl start all

# to stop all supervisor files
$ sudo supervisorctl stop all

# to restart all supervisor files
$ sudo supervisorctl restart all
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Lets configure nginx to read the gunicorn sock file. Go to nginx root directory
```bash
$ cd /etc/nginx/sites-avilable/
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create and edit the file ```django.conf```.
```bash
$ sudo nano django.conf
```
```bash
# Add the following lines

#  For daphne
upstream websocket {
    server 0.0.0.0:8001;
}

server{
        listen 80;
        server_name <IP>;
        # gunicorn sock file location
        location / {
                     include proxy_params;
                     proxy_pass http://unix:/home/ubuntu/app.sock;
        }
        # static files location
        location /static {
                      autoindex on;
                      alias /home/ubuntu/static;
        }
        # static cdn files location
        location /static_cdn {
                      autoindex on;
                      alias /home/ubuntu/static_cdn;
        }
        # media cdn files location
        location /media_cdn {
                      autoindex on;
                      alias /home/ubuntu/media_cdn;
        }
        # web socket end point for daphne
        location /chat {
        proxy_pass http://websocket;

        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        proxy_redirect off;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Host $server_name;
        }
}
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> sym link site-available with sites-enabled.
```bash
$ sudo ln django.conf /etc/nginx/sites-enabled/
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Test nginx.
```bash
$ sudo nginx -t
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Restart nginx.
```bash
$ sudo service nginx  restart
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To check nginx status.
```bash
$ sudo service nginx  status
```

Our django application will be running fine in the server now, and it is accessible.

## _5. Redis Installation and Configuration _
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To install redis.
```bash
$ sudo apt-get install redis-server
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To check redis running.
```bash
# If result is PONG. Redis running fine.
$ redis-cli ping
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> To auto-restart redis on server restart.
```bash
$ sudo systemctl enable redis-server.service
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> In case Redis and Django application runs in different server instances. Then do the following
```bash
$ sudo nano /etc/redis/redis.conf
```
```bash
# Find out the following lines in the file and modify as given below

# From
bind 127.0.0.1

# TO
bind 0.0.0.0
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Now restart the redis server.
```bash
$ sudo service redis-server restart
```

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> In django application use this redis server IP where ever necessary. To check the remote redis server working.
```bash
# If you get PONG. Redis running fine and connection also fine.
$ redis-cli -h <ip> ping
```

## _6. Celery Installation and Configuration _
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Celery and its requirements were already installed from requirements.txt.

&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Let's configure the supervisor to run the celery. First configure for celery-beat. Create and edit the file ```celery_beat.conf```.
```bash
$ sudo nano /etc/supervisor/conf.d/celery_beat.conf
```
```bash
# Add the following lines
[program:celerybeat]
directory=/home/ubuntu
command=/home/ubuntu/env/bin/celery -A <projec name>.celery beat --loglevel=INFO

user=ubuntu
numprocs=1
stdout_logfile=/home/ubuntu/logs/celery/beat/beat-access.out.log
stderr_logfile=/home/ubuntu/logs/celery/beat/beat-error.err.log
stdout_logfile_maxbytes=50
stderr_logfile_maxbytes=50
stdout_logfile_backups=10
stderr_logfile_backups=10
autostart=true
autorestart=true
startsecs=10

; Causes supervisor to send the termination signal (SIGTERM) to the whole process group.
stopasgroup=true

; if rabbitmq is supervised, set its priority higher
; so it starts first
priority=999
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Now let's configure for celery-workers. Create and edit the file ```celery_worker.conf```.
```bash
$ sudo nano /etc/supervisor/conf.d/celery_worker.conf
```
```bash
# Add the following lines
[program:celery-worker]
directory=/home/ubuntu
command=/home/ubuntu/env/bin/celery -A <project-name>.celery worker --loglevel=INFO -Ofair -Q <queue name> -P prefork -c 4 --without-gossip --without-mingle --without-heartbeat --autoscale=8,2

user=ubuntu
numprocs=1
stdout_logfile=/home/ubuntu/logs/celery/high/worker-access.out.log
stderr_logfile=/home/ubuntu/logs/celery/high/worker-error.err.log
stdout_logfile_maxbytes=50
stderr_logfile_maxbytes=50
stdout_logfile_backups=10
stderr_logfile_backups=10
autostart=true
autorestart=true
startsecs=10

; Need to wait for currently executing tasks to finish at shutdown.
; Increase this if you have very long running tasks.
stopwaitsecs = 600

; Causes supervisor to send the termination signal (SIGTERM) to the whole process group.
stopasgroup=true

[group:celery-workers]
programs=celery-worker

; Set Celery priority higher than default (999)
; so, if rabbitmq is supervised, it will start first.
priority=1000

```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> If you have more than one celery worker. Do as following.
```bash
[program:celery-worker1]
directory=/home/ubuntu
command=/home/ubuntu/env/bin/celery -A <project-name>.celery worker --loglevel=INFO -Ofair -Q <queue name> -P prefork -c 4 --without-gossip --without-mingle --without-heartbeat --autoscale=8,2

user=ubuntu
numprocs=1
stdout_logfile=/home/ubuntu/logs/celery/worker1/worker-access.out.log
stderr_logfile=/home/ubuntu/logs/celery/worker1/worker-error.err.log
stdout_logfile_maxbytes=50
stderr_logfile_maxbytes=50
stdout_logfile_backups=10
stderr_logfile_backups=10
autostart=true
autorestart=true
startsecs=10

; Need to wait for currently executing tasks to finish at shutdown.
; Increase this if you have very long running tasks.
stopwaitsecs = 600

; Causes supervisor to send the termination signal (SIGTERM) to the whole process group.
stopasgroup=true

[program:celery-worker2]
directory=/home/ubuntu
command=/home/ubuntu/env/bin/celery -A <project name>.celery worker --loglevel=INFO -Ofair -Q <queue> -P prefork -c 4 --without-gossip --without-mingle --without-heartbeat --autoscale=4,1

user=ubuntu
numprocs=1
stdout_logfile=/home/ubuntu/logs/celery/worker2/worker-access.out.log
stderr_logfile=/home/ubuntu/logs/celery/worker2/worker-error.err.log
stdout_logfile_maxbytes=50
stderr_logfile_maxbytes=50
stdout_logfile_backups=10
stderr_logfile_backups=10
autostart=true
autorestart=true
startsecs=10

; Need to wait for currently executing tasks to finish at shutdown.
; Increase this if you have very long running tasks.
stopwaitsecs = 600

; Causes supervisor to send the termination signal (SIGTERM) to the whole process group.
stopasgroup=true

[program:celery-worker3]
directory=/home/ubuntu
command=/home/ubuntu/env/bin/celery -A <project name>.celery worker --loglevel=INFO -Ofair -Q <queue> -P prefork -c 4 --without-gossip --without-mingle --without-heartbeat --autoscale=4,1

user=ubuntu
numprocs=1
stdout_logfile=/home/ubuntu/logs/celery/worker3/worker-access.out.log
stderr_logfile=/home/ubuntu/logs/celery/worker3/worker-error.err.log
stdout_logfile_maxbytes=50
stderr_logfile_maxbytes=50
stdout_logfile_backups=10
stderr_logfile_backups=10
autostart=true
autorestart=true
startsecs=10

; Need to wait for currently executing tasks to finish at shutdown.
; Increase this if you have very long running tasks.
stopwaitsecs = 600

; Causes supervisor to send the termination signal (SIGTERM) to the whole process group.
stopasgroup=true

[group:celery-workers]
programs=celery-worker1,celery-worker2,celery-worker3

; Set Celery priority higher than default (999)
; so, if rabbitmq is supervised, it will start first.
priority=1000

```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create celery log file directory.
```bash
$ sudo mkdir /log/celery/worker1
$ sudo mkdir /log/celery/worker2
$ sudo mkdir /log/celery/worker3
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Then do the following for supervisor.
```bash
# To reread files
$ sudo supervisorctl reread

# To update
$ sudo supervisorctl update

# To restart all
$ sudo supervisorctl restart all

# or if not running to start
$ sudo supervisorctl status
```

## _7. Daphne Installation and Configuration _
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Install Daphne.
```bash
$ sudo apt install daphne
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Let's configure the supervisor to run daphne. Create and edit the file ```daphne.conf```.
```bash
$ sudo nano /etc/supervisor/conf.d/daphne.conf
```
```bash
# Add the following
[program:daphne]
directory=/home/ubuntu/
command=/home/ubuntu/env/bin/daphne -b 0.0.0.0 -p 8001 --proxy-headers <project name>.asgi:application
autostart=true
autorestart=true
stdout_logfile=/home/ubuntu/logs/daphne/websocket.log
redirect_stderr=true

```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Create daphne log file directory.
```bash
$ sudo mkdir /log/daphne
```
&emsp;&emsp;<img src="right-arrow.png" width="12" height="12" style="margin-right:1em; margin-top:1px;"> Then do the following for supervisor.
```bash
# To reread files
$ sudo supervisorctl reread

# To update
$ sudo supervisorctl update

# To restart all
$ sudo supervisorctl restart all

# or if not running to start
$ sudo supervisorctl status
```


<h3 style="text-align:right" >Prepared by <a href="https://github.com/AbilashEnfield" style="color:#ba2532">Abilash</a></h3>