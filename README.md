# Docker Drupal Stack

This docker compose project launches a set of Docker containers that comprise all the basics of Drupal 8 hosting: Varnish, Web (Apache), PHP (FPM), and MySQL. It also includes some essential utilities, such as: webmin, vim, git, drush, and Drupal console. The primary use case is to run Drupal on a relatively small VPS service, such as DigitalOcean.

The latest stable version is tagged 1.6-beta.

## Prerequisites
* [docker](https://docs.docker.com/engine/installation/)
* [docker compose](https://docs.docker.com/compose/install/)

## One-Line Quick Start
`export MYSQL_ROOT_PASSWORD=YOUR_PASSWORD_HERE && git clone git@github.com:runeasgar/docker_drupal_stack.git && cd docker_drupal_stack && docker-compose -p drupal --x-networking up -d`

**If some of your containers fail to start** (you can check with `docker-compose ps`), run `docker-compose -p drupal --x-networking up -d` again. There's a known [dependency issue](https://github.com/docker/compose/pull/2708) with Docker Compose.

*MySQL will store the password you provide as a part of its persistent database(s). Rebuilding or recreating the MySQL container will not affect this password, even if the environment variable changes.*

## What You Get

| Service | Container Name / Hostname | Network Port | Host Port |
| ------------- | ------------- |:-------------:|:-------------:|
| varnish | drupal_varnish_1 | 80 | 80 |
| web | drupal_web_1 | 80 | 8080 |
| webmin\* | drupal_web_1 | 10000 | 10000 |
| php_fpm | drupal_php_fpm_1 | 9000 | |
| mysql | drupal_mysql_1 | 3306 | |

\* *Note that webmin is not a docker service - it runs as part of the web service.*

## Now that you have your stack
* You can set up Drupal 8, which is pre-installed on the web at `/var/www/html`. You'll need to [create a MySQL database](https://www.drupal.org/documentation/install/create-database#mysql_command) and user for it. Specify `@'%'` for your database users, since your database server is technically "remote".
* [Manage](https://help.ubuntu.com/lts/serverguide/httpd.html) your Apache server if you want to create additional sites. To disable the default site, move /etc/apache2/sites-enabled/0000-default.conf to sites-available. Do NOT remove /var/www/html (I have a script that relies on its existence).
  * BETA: You can do this with webmin! It's running on port 10000 (use https). You'll need to create a user for it:
    * `docker exec -it drupal_web_1 bash`
    * `adduser webmin`
    * `adduser webmin sudo`

## Basic docker and docker-compose commands

| Command | Action |
| ------------- | ------------- |
| `docker-compose ps` | see the status of all project containers |
| `docker-compose stop` | stop all project containers  |
| `docker-compose restart` | restart all project containers |
| `docker-compose start` | start all project containers |
| `docker exec -it CONTAINER_NAME command arg1 arg2` | run a command in the specified container |
| `docker exec -it CONTAINER_NAME bash` | open a shell to the specified container |
| `docker --help` | get help with docker commands |
| `docker-compose --help` | get help with docker-compose commands |

## Using the included scripts + some shortcuts
Add the following to .bashrc on your docker host, replacing values as indicated:

```bash
# Open a bash shell to service. Usage: dockb servicename
alias dockb='/path/to/repo/scripts/dockb.sh'
# Get resource usage stats on all project containers
alias dockcs='docker stats drupal_varnish_1 drupal_web_1 drupal_php_fpm_1 drupal_mysql_1'
# Run a drush command against the default Drupal site
alias drushw='docker exec -it drupal_web_1 drush -r /var/www/html'
# Shorthand for docker-compose
alias dockc='docker-compose'
# With -r, stop and remove all project containers, with -i remove all dangling images, with -v remove all dangling volumes
alias docker-clean='/path/to/repo/scripts/docker-clean.sh'
```

Then, run `source ~/.bashrc` to activate these aliases.

## Docker Documentation
* [Docker User Guide](https://docs.docker.com/engine/userguide/)
* [Overview of Docker Compose](https://docs.docker.com/compose/)

See the "Issues" tab for a list of known issues and future improvements.
