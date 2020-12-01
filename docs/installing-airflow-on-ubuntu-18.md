---
date: 2019-12-16
---

# Installing Airflow 1.10 on Ubuntu 18

This guide walks through installing Airflow on a Ubuntu 18.04 server. It assumes you can access a web server with a fresh Ubuntu install as the root user.

This guide also expects that you're generally familiar with the concepts behind Airflow, like executors, DAGs, and Hooks, but it's not necessary to be an Airflow expert. If you've just started using Airflow, it's helpful to complete the project's quickstart tutorial first, before attempting to deploy a production instance of Airflow.

By the time you've finished this guide, you'll have created the following:

- An Airflow Webserver that's accessible through a web browser. Optionally, we'll show how to enforce HTTPS connections to the Airflow Webserver if you've purchased a custom domain name.
- An Airflow Scheduler that uses the Celery executor. We'll use a single-node setup, but using the Celery executor will make it easier for you to adapt to a multiple node setup later, and it will also enable you to trigger DAG runs manually through a web browser.

Depending on the cloud hosting provider you've chosen for your Ubuntu server, the exact steps for configuring the Webserver will be slightly different, and we can't provide detailed instructions for all the possible vendors. This guide will provide instructions if you're using AWS EC2 instance, but the steps are similar if you're on Digital Ocean, Linode, or another provider.

We recommend using a virtual server with at least 2 CPUs and 4G of RAM, but the instructions below should work on smaller instances, too.

So, let's get started!

### Install Airflow dependencies: Postgres, Python3, and Redis

For this tutorial we'll use Python 3 and a Postgres database for Airflow's metadata.

```sh
sudo apt-get update
sudo apt-get install -y \
  python3-pip \
  postgresql \
  postgresql-contrib \
  redis \
  nginx
```

The Postgres metadata database is necessary for Airflow to record the state of each task that it runs. The database can also securely store credentials that allow Airflow to connect to other systems, such as Salesforce, S3, or Redshift.

Redis is necessary to allow the Airflow Celery Executor to orchestrate its jobs across multiple nodes and to communicate with the Airflow Scheduler.

Nginx will be used as a reverse proxy for the Airflow Webserver, and is necessary if you plan to run Airflow on a custom domain, such as airflow.corbettanalytics.com.

#### Configure Postgres

When you installed Postgres it also installed commands for creating new Postgres databases and users. Create a new database and user for Airflow, and name them both airflow.

```
sudo -u postgres bash -c "createdb airflow"
sudo -u postgres bash -c "createuser airflow --pwprompt"
```

The createuser command will prompt you for a password for the airflow user. Later, we'll need to store that password in Airflow's configuration file in order for Airflow to function properly.

#### Create a non-root user for Airflow

Running Airflow as root is risky, since you'll be executing code from anyone at your company. If they make a mistake and damage the server by accidentally deleting any important files, you'll have to re-provision a new instance. To reduce the chance that happens, we'll create a non-root user to run Airflow.

```
sudo adduser airflow
```

Switch users to the airflow user and cd into their home directory.

```
su airflow
cd ~/airflow
```

###3 Install Airflow with pip

We're now ready to install Airflow. Note that the package's name is actually apache-airflow and that we're including the optional Postgres and Celery dependencies by appending `[postgres,celery]` to the package name. We've also included the package email_validator, which is necessary for authenticating with the Airflow webserver.

```
pip3 install --user apache-airflow[postgres,celery,redis] email_validator
```

On Ubuntu18 pip will install the Airflow executable in `/home/ubuntu/.local/bin`, but by default that directory is not searched when running commands.

To fix this, add the `.local/bin` directory name to your `$PATH` variable.

```
echo "export PATH=$PATH:$HOME/.local/bin" >> ~/.bashrc
source ~/.bashrc
```

You should now be able to access the Airflow cli.

```
airflow version  # -> 1.10.10
```

Note that most of Airflow CLI's commands will be broken at this point because the Airflow metadata database isn't configured. To fix this, we'll need edit Airflow's configuration file.

### Configure Airflow

By default, Airflow will look for its configuration in a file located at `$HOME/airflow/airflow.cfg`. We need to make several changes to that file in order for Airflow to function correctly.

- Metadata database connection: We need to tell Airflow how to access its metadata database, which we do by setting the sql_alchemy_conn value. That will point to the local Postgres installation we previously configured.
- Webserver authentication: Airflow supports a few different authentication schemes, but we'll use the default which just requires a username and password.
- Celery Executor selection: Airflow uses the SequentialExecutor by default, but we'll change that to use the more useful CeleryExecutor.

To make these changes, update the following values in the airflow.cfg file.

```
[core]
# Connection string to the local Postgres database
sql_alchemy_conn = postgresql://airflow:password@localhost:5432/airflow

# Class name of the executor
executor = CeleryExecutor


[webserver]
# Run a single gunicorn process for handling requests.
workers = 1

# Require password authentication to the webserver
authenticate = True
rbac = True


[celery]
broker_url = redis://localhost:6379/0
result_backend = sqla+postgres://airflow:password@localhost:5432/airflow
```

Note that it's important you make these changes in the correct sections - ensure you're replacing the existing values in the configuration, rather than pasting in new lines. If you place the `authenticate = True` value the `[core]` section, Airflow won't know to require authentication for the Webserver.

### Initialize the Airflow metadata database

Airflow is now ready to initialize its metadata database.

```
airflow initdb
```

You'll see Airflow log some information about the migrations that it's running on the database. To see the list of tables that Airflow created, use the following command.

```
psql -c '\dt'
```

At this point the Airflow installation is working correctly, and we're ready to add the first Airflow user.

#### Create an Airflow user

The Airflow CLI provides a create_user command to create a user that can log into the airflow Webserver.

```
airflow create_user \
  --username admin \
  --role Admin \
  --email tom@corbettanalytics.com \
  --firstname Tom \
  --lastname Waterman \
  --password {{ SET-PASSWORD-HERE }}
```

You'll now be able to log in with the username and password just configured. Make sure to modify the email and password in the command above to your own values.

#### Start the airflow services

For our installation, we'll need to run three Airflow services: the Webserver, the Scheduler, and the Worker.

- The scheduler is responsible for determining whether a DAG should be executed.
- The webserver is responsible for creating the DAG visualizations and "front end" that you can access from the web browser. If your DAGs are not running, it is likely an issue with the scheduler and not the webserver.
- The worker is responsible for actually executing the jobs in a DAG. After a working finishes running a DAG's job, it will log the status of the job in the Airflow metadata database.

Start the Airflow services now. We'll add the --daemon flag to run the processes as daemons, so they'll continue running even after you log off.

```
airflow webserver --daemon
airflow scheduler --daemon
airflow worker --daemon
```

You can test that the webserver is running locally. Use curl to test that the Webserver replies with a valid response: you should get a 301 status code redirecting you to /home or /login.

```
curl http://0.0.0.0:8080
```

At this point, you won't be able to access the Airflow Webserver from your web browser. To access the webserver through your browser, you'll need to have Nginx forward requests to the application.

### Configure Nginx

Switch back to the Ubuntu root user by typing exit. Run the following command to confirm you're no longer using the non-root airflow user.

```
whoami  # -> ubuntu
```

Create a new Nginx config file in the `/etc/nginx/sites-available` directory named `airflow`.

```
sudo vi /etc/nginx/sites-available/airflow
```

Paste the following into that file. Update the values for `server_name` with your own server's domain or IP address.

```
server {
  listen 80;
  listen [::]:80;

  # Replace with your own server's URL and/or IP address. Note that
  # supplying a single value, such as an IP address, is okay.
  server_name airflow.corbettanalytics.com 34.218.228.207;

  location / {
    proxy_pass http://0.0.0.0:8080
  }
}
```

To enable Nginx to use that site's configuration, link the file into the `/etc/nginx/sites-enabled` directory.

```
sudo ln -s /etc/nginx/sites-available/airflow /etc/nginx/sites-enabled
```

Additionally, you'll want to update the `/etc/nginx/nginx.conf` file if you're using a custom domain to host your Airflow server. The follow parameters enables you to use a custom domain, such as airflow.corbettanalytics.com.

```
http {
     server_names_hash_bucket_size 256;
}
```

Restart the Nginx service for the new configuration to take effect.

```
sudo systemctl restart nginx
```

You should now be able to access the Airflow web interface by typing your server's IP address or domain name into your browser's address bar.

**Note #1**: to access Airflow through the custom domain name, ensure you've already set up an A-record with your DNS provider that points to your server's IP address. If you're using AWS's Route 53, create those records now in the Route 53 console.

**Note #2**: if you're running the server on AWS, you'll also need to update your server's security group to allow traffic to allow public access to port 80 and port 443. Follow the instructions from AWS's knowledge center to allow access to your server over the public internet.

### Optional: enforce HTTPS only

To enforce HTTPS connections, you'll need to have configured a custom domain that correctly points to your server. Ensure that you can load the Airflow Webserver at your custom domain before proceeding. For example, I was able load Airflow at http://airflow.corbettanalytics.com before configuring HTTPS only connections.

Install the Let's Encrypt certbot.

```
sudo apt-get install software-properties-common
sudo add-apt-repository universe
sudo add-apt-repository ppa:certbot/certbot
sudo apt-get update
```

Start the process to automatically configure HTTPS connections to your server.

```
sudo certbot --nginx
```

The Certbot command will prompt you with a series of questions. It should automatically detect that the certificates should be generated for your domain custom domain. For example, Certbot displayed the following details for airflow.corbettanalytics.com.

```
Which names would you like to activate HTTPS for?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
1: airflow.corbettanalytics.com
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
You should also ensure that you select the option for Certbot to update the Nginx configuration to redirect HTTP requests to HTTPS requests. Certbot will make the necessary changes to the etc/nginx/nginx.conf file and then restart Nginx automatically.
```

If everything has been configured correctly, you should now be able to access your Airflow Webserver over HTTPS.