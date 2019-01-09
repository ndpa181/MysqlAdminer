# MysqlAdminer
===============

Quickly install the latest version of Mysql8 with Docker with just one command, and use Adminer to easily manage the database via web pages. Navicat or other third-party MySQL client connections are also OK.

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
// # (for centos7) chown polkitd:ssh_keys log -R 
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

1. It takes tens of seconds to run for the first time, and logging in to MySQL before successful startup will fail. It’s getting faster from the second time. When you run the command "sudo docker-compose up" for the first time without the "-d" parameter, you can see the startup process information on the terminal, which helps to track the error.

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

3. A ^M error message appears while the admin script is running.
The line breaks used by text files are different under each operating system. UNIX/Linux uses 0x0A(LF), and earlier Mac OS used 0x0D(CR), and later OS X was consistent with UNIX after replacing the kernel. However, DOS/Windows always uses 0x0D0A (CRLF) as a newline. Git provides a "newline automatic conversion" feature. This feature is in "automatic mode" by default. When you check out a file, it tries to replace the UNIX newline character (LF) with the Windows newline character (CRLF); when you submit the file, it tries to replace CRLF with LF. Git's "Line Feed Auto-Conversion" feature sounds smart and intimate because it attempts to maintain file consistency (UNIX style) on the one hand and local file compatibility (Windows style) on the other. Unfortunately, this feature is buggy and is unlikely to be fixed in the short term.

Solution: Check gitconfig before git clone. 
```
git config --global core.autocrlf false
git config --global core.safecrlf true
```


