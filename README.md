# Docker moodle mariadb

Fork of Dockerize-Moodle-MariaDB

## .env file
Add a .env file
```
touch .env
```
Add inside:
DOMAIN_SERVER_BASE=your-domain.org
MOODLE_DATA_FOLDER=/volume/path/moodle/moodledata

## Start

1. docker-compose up -d
2. Access on localhost:9992


## Deploy behind Reverse Proxy
```
If you want to deploy behind Revere proxy (https://github.com/gilangvperdana/Multiple-Https-Nginx-withOneServer)
Please to change some configuration inside container :

/var/www/html/moodle/config.php :
$CFG->wwwroot   = 'https://your.domain.com';
$CFG->sslproxy = true;

and Comment 3-line on index.php (var/www/html/moodle/admin/index.php) :
Comment in line 753-755,

//        if ($adminuser->lastip !== getremoteaddr()) {
//           print_error('installhijacked', 'admin');
//        }
        
Refresh your web, and happy deploying!
```

## Backup docker image

```
Get moodle container ID 
sudo docker ps -a

Create a container copy
sudo `docker commit ce7d53e7687b moodle01`
docker save -o moodle_20220902.tar moodle01
```

## Access to container by terminal
```
docker exec -ti moodle /bin/bash
```

Set max_input_vars to 5000
```
nano /etc/php/7.4/apache2/php.ini 
```
and set:
```
max_input_vars = 5000
```

## Backup

### Database
```
docker exec -i mariadb mariadb-dump -h127.0.0.1  -uroot -p$MYSQL_ROOT_PASSWORD moodle > docker_moodle.sql
```

### Moodledata

```
sudo tar zcvf moodledata.tgz /home/damufo/data/moodle/moodledata
```

## Set cron

In this container the command is:

```
/usr/bin/php /var/www/html/moodle/admin/cli/cron.php
```

Can execute from container terminal to check. If work

Execute inside container terminal:

```
crontab -u www-data -e
```

and add in the last of document:
```
* * * * * /usr/bin/php  /var/www/html/moodle/admin/cli/cron.php
```

Check if the cron service is active (Debian)
```
service cron status
```

If not:
```
service cron start
```

