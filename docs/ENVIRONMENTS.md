# Navigation
 - [Domain envs](#domain-envs)
 - [Project envs](#project-envs)
 - [Images envs](#images-envs)
 - [Server envs](#server-envs)
 - [Mysql envs](#mysql-envs)
 - [Redis envs](#redis-envs)
 - [FTP envs](#ftp-envs)
 - [SSH envs](#ssh-envs)
 - [PHP-FPM envs](#php-fpm-envs)
 - [Testing envs](#testing-envs)
 - [Auto update hosts file (host machine)](#auto-update-hosts-envs)
 - [Auto create nginx proxy (host machine)](#auto-create-proxy-envs)
 - [Deploy](#deploy)

## <a id="domain-envs"></a>Domain envs (service: nginx, server)
Property | Values | Description
---------|--------|------------
`DOMAIN_COMMON` | `(string)` `Default: not set` | Site common domain. (e.g. sample.io)
`DOMAIN_ADMIN` | `(string)` `Default: not set` | Site common domain. (e.g. admin.sample.io)
`DOMAIN_API` | `(string)` `Default: not set` | Site api domain. (e.g. api.sample.io)
`DOMAIN_COVERAGE` | `(string)` `Default: not set` | Site coverage domain. (e.g. coverage.sample.io)
`DOMAIN_OPCACHE` | `(string)` `Default: not set` | Site opcache domain. (e.g. opcache.sample.io) 
`DOMAIN_CUSTOM_NAME` | `(string)` | Custom domain name. Create nginx config with `# <domains-include>DOMAIN_COMMON</domains-include>` and `server_name  $COMMON_DOMAIN;` or copy from `vendor/matthew-p/docker-server/docker/nginx/templates/domain.conf` to `docker/nginx/conf-dynamic.d/custom.conf` and change. 
`SSL_DOMAINS` | `(array)` `Default: not set` | Config letsencrypt ssl domains. Example: `SSL_DOMAINS[0]="admin@sample.io :sample.io api.sample.io admin.sample.io:"`
`DOMAIN_DEFAULT` | `(string)` `Default: not set` | Set default server domain

## <a id="project-envs"></a>Project env (service: all)
Property | Values | Description
---------|--------|------------
`PROJECT_ENVIRONMENT` | `DEV`/`PROD` | Project environment: Include different nginx ssl certificates (letsencrypt), toggle php xdebug, nginx cache control, rewrite to https 
`PROJECT_NAME` | `(string)` | Unique project name
`COMPOSE_PROJECT_NAME` | `(string)` `Default: $PROJECT_NAME` | Docker compose project name. Is is recommended to be equal `PROJECT_NAME`. [see more](https://docs.docker.com/compose/reference/envvars/#compose_project_name)
`PROJECT_ROOT_CONTAINER` | `(string)` `Default: /var/www/html` | Project root, inside server container. It is not recommended to change
`PROJECT_ROOT`| `(string)` `Default: ${VENDOR_PARENT_DIR}` | Project root in host machine. This can be changed for create task definition in aws and etc.
`PACKAGE_DOCKER_FOLDER`| `(string)` `Default: ${PROJECT_ROOT}${VENDOR_COMMON_DIR}/docker` | This is the path to the vendor package **docker** folder `vendor/matthew-p/docker-server/docker`
`PACKAGE_DOCKER_FOLDER_CONTAINER` | `string` `Default: /docker-server` | This is the path to the mounted **docker** folder on container
`PROJECT_DOCKER_FOLDER` | `(string)` `Default: ${PACKAGE_DOCKER_FOLDER}` | This is the path to the mounted **project docker** folder on container. Example: `volumes: - ${PROJECT_DOCKER_FOLDER}/nginx/logs:/var/log/nginx` it will be save nginx logs to your project docker folder.
`PROJECT_DOCKER_FOLDER_CONTAINER`| `(string)` `Default: ${PROJECT_ROOT_CONTAINER}/docker` | **project docker folder** in docker container.
`RUN_SERVER_COMPOSER` | `1`/`0` `Default: 0` | Require globaly composer asset plugin and run composer install in project root after server start (inside docker container)
`GIT_AUTHTOKEN` | `(string)` `Default: not set` |  Github auth token (set if `RUN_SERVER_COMPOSER=1`). See [Github token](https://github.com/settings/tokens)
`DEFAULT_DETACHED_MODE`| `(string)` `Default: -d` |  Automatically added `-d` to `docker-compose up` (and `composer server up`)
`PROJECT_ENV_PATH_FORCE`| `(string)` `Default: not set` | Use only for generate .env without run server (e.g. create aws definition)
`SERVICES`| `(string)` | All path to docker compose files which need project

## <a id="images-envs"></a>Images env (service: all)
Property | Values | Description
---------|--------|------------
`NGINX_REPOSITORY`| `(string)` `Default: matthewpatell/universal-docker-nginx:3.7` | Nginx docker image
`SERVER_REPOSITORY`| `(string)` `Default: matthewpatell/universal-docker-server:4.0-dev` | Either server (or server dev) image in non-scalable configuration, or `matthewpatell/universal-docker-server-php-fpm` for scaling `nginx`+`php-fpm`.
`SERVER_NODE_REPOSITORY`| `(string)` `Default: node:13.10-alpine` | Either server image for scaling `nginx`+`node`.
`TERMINAL_REPOSITORY`| `(string)` `Default: none` | Static non-scalable docker image for ssh connection, cron, queue listener and etc. Use image: `matthewpatell/universal-docker-server`.

## <a id="server-envs"></a>Server env (service: server)

`-f !!docker-compose.nginx.yml` add to `SERVICES` (default: added)

Property | Values | Description
---------|--------|------------
`SERVER_HTTP_PORT`| `(int)` `Default: 80` | Server http port. Used when `PROJECT_ENVIRONMENT=DEV`
`SERVER_SSL_PORT`| `(int)` `Default: 443` | Server https port. Used when `PROJECT_ENVIRONMENT=PROD`
`NGINX_LIMIT_KEY`| `(string)` `Default: autogenerated` | Key for disable nginx limits (limit_req_zone)

## <a id="mysql-envs"></a>Mysql env (service: mysql)

`-f !!docker-compose.mysql.yml` add to `SERVICES` (default: added)

Property | Values | Description
---------|--------|------------
`MYSQL_HOST`| `(string)` `Default: mysql` | Mysql host (internal)
`MYSQL_PORT`| `(int)` `Default: 3306` | Mysql port (external)
`MYSQL_ROOT_PASSWORD` | `(string)` | Mysql root password
`MYSQL_DATABASE` | `(string)` | Mysql database (automatically created at first run)
`MYSQL_USER` | `(string)` | Mysql user
`MYSQL_PASSWORD` | `(string)` | Mysql password

## <a id="redis-envs"></a>Redis env (service: redis)

`-f !!docker-compose.redis.yml` add to `SERVICES` (default: added)

Property | Values | Description
---------|--------|------------
`REDIS_HOST`| `(string)` `Default: redis` | Redis host (internal)
`REDIS_PORT`| `(int)` `Default: 6379` | Redis port (external)
`REDIS_PASSWORD` | `(string)` | Redis password
`REDIS_DATABASE` | `(string)` | Redis database

## <a id="ftp-envs"></a>FTP env (service: ftp)

`-f !!docker-compose.ftp.yml` add to `SERVICES` (default: not added)

Property | Values | Description
---------|--------|------------
`FTP_DB_TABLE_NAME`| `(string)` `Default: ftp_users` | Mysql table name where stored ftp users
`FTP_PORT_BIND`| `(int)` `Default: 21` | FTP primary port
`FTP_PASSIVE_PORT_1-10` | `(int)` `Default: 30000-30009` | FTP passive ports 
`FTP_SUBJECT_COUNTRY` | `(string)` | Country code (ISO 3166-1 alpha-2). For SSL
`FTP_SUBJECT_CITY` | `(string)` | City name. For SSL
`FTP_SUBJECT_ORGANIZATION` | `(string)` | Organization name. For SSL
`FTP_SUBJECT_DOMAIN` | `(string)` | Organization name. For SSL

## <a id="ssh-envs"></a>SSH env (service: server)

`-f !!docker-compose.ssh.yml` add to `SERVICES`

Property | Values | Description
---------|--------|------------
`SSH_PORT`| `(int)` `Default: 22` | SSH port (external)
`SSH_PASSWORD`| `(string)` | SSH password

## <a id="php-fpm-envs"></a>PHP-FPM env (service: server)

`-f !!docker-compose.php-fpm.yml` add to `SERVICES` (default: added)

Property | Values | Description
---------|--------|------------
`DEBUG_PORT`| `(int)` `Default: 9000` | PHP debug port
`PHP_IDE_CONFIG`| `(string)` `Default: "serverName=${PROJECT_NAME}`" | IDE Config
`XDEBUG_CONFIG`| `(string)` | Xdebug config

## <a id="testing-envs"></a>Testing env (service: server)

`-f !!docker-compose.tests.yml` add to `SERVICES` 
See [example CI config](CI-EXAMPLE.md)

Property | Values | Description
---------|--------|------------
`TELEGRAM_BOT_TOKEN`| `(string)` `Default: not set` | Telegram bot token for send crash tests (CI)
`TELEGRAM_CHAT_ID`| `(string)` `Default: not set` | Telegram chat ID for send crash tests (CI)

## <a id="auto-create-proxy-envs"></a>Auto create nginx proxy env (host machine)
Property | Values | Description
---------|--------|------------
`HOST_NGINX_PROXIES`| `yes`/`no` `Default: no` | Create nginx proxy config file automatically (proxy to docker container nginx)
`HOST_NGINX_KEEP_CONF`| `yes`/`no` `Default: no` | Set `yes` for keep created nginx proxy conf file after `server down`
`HOST_NGINX_TEMPLATE_PATH`| `(string)` `Default: ${VENDOR_DIR}/conf/nginx-proxy.conf` | Template nginx proxy conf
`HOST_NGINX_CONF_DIR`| `(string)` `Default: /etc/nginx/sites-enabled` | Nginx configs dir on host machine
`HOST_NGINX_RESTART_COMMAND`| `(string)` `Default: "service nginx restart"` | Restart nginx command on host machine
`NGINX_DEFAULT_HOST` | `yes`/`no` `Default: no` | Enable/disable default nginx host

## <a id="auto-update-hosts-envs"></a>Auto update hosts env (host machine)
Property | Values | Description
---------|--------|------------
`HOST_ETC_HOSTS_UPDATE`| `yes`/`no` `Default: no` | Update `/etc/hosts` automatically
`HOST_ETC_KEEP_CONF`| `yes`/`no` `Default: no` | Set `yes` for keep generated hosts after `server down`
`HOST_ETC_HOST_PATH`| `(string)` `Default: /etc/hosts` | Path to hosts file
`HOST_ETC_HOST_IP`| `(string)` `Default: Not set` | IP for hosts file. Get automatically for `nginx` container. Set to `127.0.0.1` for Mac OS

## <a id="auto-update-hosts-envs"></a>AWS Commands env (service: all)

AWS COMMANDS (`-SERVICES-` will be replaced)  
`-` will be replaced to `_` (in composer command)  
**YOU NEED SET `PROJECT_ENV_PATH_FORCE`** Example: _/aws-drive/site-root_

[See example values](../docker/.env-default)

Property | Values | Description
---------|--------|------------
`AWS_UPDATE_TASK`| `(string)` | Create/update task definition on aws. Console command: `composer server-prod update-task`
`AWS_UPDATE_ENV`| `(string)` | Create/update `.env` file on remote host. Console command: `composer server-prod update-env`

## <a id="deploy"></a>Deploy
Property | Values | Description
---------|--------|------------
`DEPLOY_SERVER_NAME`| `string` | SSH server name.
`DEPLOY_CONTAINER_NAME`| `string` | Docker container name for deploy.
`PROJECT_PATH`| `(string)` | Path to project folder in remote server.
`DEPLOY_STRATEGY`| `(string)` `1-4` | See `site-deploy.sh` for more info
