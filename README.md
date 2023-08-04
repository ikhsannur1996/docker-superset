Creating a Docker Image for Apache Superset

This document provides instructions to build a Docker image for Apache Superset, an open-source business intelligence web application. With this Docker image, you can easily deploy and run Superset in a containerized environment.

Prerequisites

Docker installed on your system.
Getting Started

Download the Dockerfile:
Download the Dockerfile from the repository or copy the contents below into a file named Dockerfile:

Dockerfile
Copy code
# Dockerfile for Apache Superset
FROM apache/superset

# Switching to root to install the required packages
USER root

# Example: installing the MySQL driver to connect to the metadata database
# if you prefer Postgres, you may want to use psycopg2-binary instead
RUN pip install mysqlclient

# Example: installing a driver to connect to Redshift
# Find the appropriate driver for your analytics database from
# https://superset.apache.org/installation.html#database-dependencies
RUN pip install sqlalchemy-redshift

# Switching back to using the superset user
USER superset
Building the Docker Image

To build the Docker image for Apache Superset, run the following command in the directory where the Dockerfile is located:

bash
Copy code
$ docker build -t superset .
Running Apache Superset Container

To start a Superset instance on port 8080, use the following command:

bash
Copy code
$ docker run -d -p 8080:8088 -e "SUPERSET_SECRET_KEY=<your_secret_key_here>" --name superset superset
Note: Replace <your_secret_key_here> with a randomly generated secret key to ensure the security of your Superset instance. You can use Python to generate a random key:

bash
Copy code
$ python -c 'import os; print(os.urandom(16).hex())'
Initializing Superset

Once the container is running, you need to initialize the Superset instance by performing the following steps:

Step 1: Create a Local Admin Account

Run the following command to set up a local admin account:

bash
Copy code
$ docker exec -it superset superset fab create-admin \
    --username admin \
    --firstname Superset \
    --lastname Admin \
    --email admin@superset.com \
    --password admin
Replace admin with your desired username and admin@superset.com with your email address. Choose a strong password for better security.

Step 2: Migrate the Local Database

Next, migrate the local database to the latest version:

bash
Copy code
$ docker exec -it superset superset db upgrade
Step 3: Load Example Data

Load example data to have some sample visualizations and dashboards:

bash
Copy code
$ docker exec -it superset superset load_examples
Step 4: Setup Roles

Set up the roles required for Superset:

bash
Copy code
$ docker exec -it superset superset init
Accessing Superset

After completing the initialization steps, you can access the Superset web interface by navigating to http://localhost:8080/login/. Use the admin credentials (username: admin, password: admin) or the credentials you set during the admin account creation to log in.

Extending the Image

This Docker image contains only the base Superset build and excludes database drivers needed to connect to analytics databases like MySQL, Postgres, BigQuery, Snowflake, Redshift, etc. To extend this image with the required drivers, use the Dockerfile provided above and add the necessary driver installation commands.

References

For more information and documentation on Apache Superset, visit the official website: https://superset.apache.org

Happy data exploration with Apache Superset in your Docker container! 🚀
