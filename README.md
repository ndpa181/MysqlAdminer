# MysqlAdminer
===============

Quickly install Mysql with Docker in one sentence, easy to manage database with Adminer. 
*Make the world simpler*

Table of contents
-----------------
* [Installation](#installation)
* [Configuration](#configuration)
* [Optional: Alias](#optional-alias)
* [Uninstall](#uninstall)
* [Troubleshooting](#troubleshooting)

Installation
------------

Make sure you have the Docker Compose installed. You can find installation instructions at https://docs.docker.com/compose/install/

install
```
git clone https://github.com/ndpa181/MysqlAdminer mysql
cd mysql
sudo docker-compose up -d
```

shell login

```Bash
./admin
```
web login
```
http://localhost:81
```

Configuration
-------------

Change your default database name, data-directory, port  in `docker-compose.xml`.
```
    db:
    ...
    volumes:
        - "./data:/var/lib/mysql" # mysql data diretory
        - "./mysql:/etc/mysql"    # mysql configure
      ports:
        - "3306:3306"             # mysql port 
    ...
    adminer:
    ...
    ports:
      - 81:8080                   # web access port = 81
    ...
```

After the initial successful installation and login, You may change the password by 
Using :

```Bash
sudo docker exec -it mysql mysqladmin -u root --password='OLD_PASSWORD' password 'NEW_PASSWORD';
```
 Or :
 ``` SQL
use mysql；
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'YOUR_NEW_PASSWORD';
FLUSH PRIVILEGES;
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'YOUR_NEW_PASSWORD';
FLUSH PRIVILEGES;
 ```

 new login comnand line may like :
```
sudo docker exec -it mysql mysql -uroot -p'YOUR_NEW_PASSWORD'
```

Optional: Alias
---------------
add these lines to /etc/profile
```
alias dk='sudo docker'
alias dki='dk inspect -f "{{.Config}}" '
alias dkinfo='dk info'
alias docker-clear='dk rm $(docker ps -a -q)'
alias dkc='sudo docker-compose'
alias dkcu='dkc up -d'
alias dkcd='dkc down'
alias dkcr='dkc restart'

alias dka='dk ps --format "{{.ID}}: {{.Command}}\t {{.Names}}     \t{{.Status}} \t{{.Ports}}" '
alias dkps='dk ps --format "table {{.ID}} {{.Command}}\t{{.Ports}}\t{{.Names}}"'
alias de='dk exec'

alias mysql='de -it mysql mysql'
alias mysqladmin='de -it mysql mysqladmin'
alias mysqldump='de -it mysql mysqldump'
```

```bash
souce /etc/profile
```

Uninstall 
---------------

uninstall
```
cd mysql
dkcd
docker-clear
dk rmi mysql
dk rmi adminer
cd ..
rm -rf mysql
```

Troubleshooting
---------------

1. It takes tens of seconds to add -d for the first time. When you run the command "sudo docker-compose up" for the first time without the "--d" parameter, you can see the startup process information on the terminal, which helps to track the error.

2. Check if the container is running normally.
docker check status command:
```
$ sudo docker inspect --format='{{.State.Status}}'  mysql

running  /* normal  */
```


```
$ sudo docker inspect --format='{{.State.Status}}'  mysql

Template parsing error: ...  /* sth wrong*/
```




