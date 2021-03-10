# Real World Project: Database Shard
```
This project uses latest version of MariaDB MaxScale on the latest version of Ubuntu.
```

## The Pre-requisites:

Have PC or VM running current version of Ubuntu. *You can install an earlier version and update later if unable to grab latest version*

```
Install Docker 
Docker Compose 
Mariadb

```

## To install Docker on VM or PC:

#Commands used in terminal:
```
sudo apt update *This command will update to the latest version of the OS (refrence to the Pre-requisites above ^)
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable"

sudo apt install docker-ce
```
## Next we will confirm Docker Status with:
```
sudo systemctl status docker
```

## Installing Docker Compose:
```
sudo apt install docker-compose
```

## To install Mariadb:
```
sudo apt install mariadb-client
```

## Cloning the repository:
Next we will be grabbing the repository from Git-Hub by running:
```
git clone https://github.com/ThaHolyPaladin/maxscale-docker
```

## Changing directories:
Once you grab the respository from Git-Hub we will change into the new directory by:

```
cd maxscale-docker/maxscale/
```

## Bringing up the containers:

We will then input the command to bring up the containers to confirm the git clone was sucessfull 
```
docker-compose up -d
```
## Confirm that the three containers imported correctly as shown:
```
docker-compose up -d
```

```
maxscale_master2_1    docker-entrypoint.sh mysql ...   Up      0.0.0.0:4003->3306/tcp                                  
maxscale_master_1     docker-entrypoint.sh mysql ...   Up      0.0.0.0:4001->3306/tcp                                  
maxscale_maxscale_1   /usr/bin/tini -- docker-en ...   Up      3306/tcp, 0.0.0.0:4000->4000/tcp, 0.0.0.0:8989->8989/tcp
```

## Running Containers:
We will then confirm that they are running by using the command:

```
docker-compose exec maxscale maxctrl list servers
```
Which will bring up

```
┌────────────────┬─────────┬──────┬─────────────┬─────────────────┬───────────┐
│ Server         │ Address │ Port │ Connections │ State           │ GTID      │
├────────────────┼─────────┼──────┼─────────────┼─────────────────┼───────────┤
│ zip_master_one │ master  │ 3306 │ 0           │ Master, Running │ 0-3000-32 │
├────────────────┼─────────┼──────┼─────────────┼─────────────────┼───────────┤
│ zip_master_two │ master2 │ 3306 │ 0           │ Running         │ 0-3000-31 │
└────────────────┴─────────┴──────┴─────────────┴─────────────────┴───────────┘
```

## Logging in to Database

Now that we can see the zip masters running we will now log into the database with:

```
mariadb -umaxuser -pmaxpwd -h 127.0.0.1 -P 4000
```

You will then be logged in and be prompted with

```
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 1
Server version: 10.5.8-MariaDB-1:10.5.8+maria~focal-log mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> 
```

## Commands to run while in database

Now that we are in we want to confirm that the information is there with:

```
show databases;
```

This will bring up the databases in the mariadb

```
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| zipcodes_one       |
| zipcodes_two       |
+--------------------+
```

## From there we will select the zipcode one and show the last 10 of zipcode one data:

```
SELECT * FROM zipcodes_one.zipcodes_one LIMIT 9990,10;
```

while ```SELECT * FROM zipcodes_two.zipcodes_two LIMIT 10;```

will show the first 10 rows from zipcode two. 

## Viewing the largest and smallest zipcode:

Now that we can organize the data we will then find out the largest zipcode from zipcode one by using 

```
SELECT Zipcode FROM zipcodes_one.zipcodes_one ORDER BY Zipcode DESC LIMIT 1;
```

## And vice versa we will find the smallest from zipcode two with:

```
SELECT Zipcode FROM zipcodes_two.zipcodes_two ORDER BY Zipcode ASC LIMIT 1;
```

### Now that we are all finished we will remove the clusters and containers with:

```
docker-compose down -v
```

### Sources used:

(https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-18-04)

(https://docs.docker.com/compose/install/)

(https://mariadb.com/kb/en/mariadb-maxscale-25-simple-sharding-with-two-servers/)

https://docs.docker.com/engine/install/ubuntu/

https://www.digitalocean.com/community/tutorials/how-to-remove-docker-images-containers-and-volumes

