Universal docker server
===========================
PHP FPM, Nginx  

Installation
------------

The preferred way to install this extension is through [composer](http://getcomposer.org/download/).

Either run

```
php composer.phar require --prefer-dist matthew-p/docker-server "@dev"
```

or add

```
"matthew-p/docker-server": "@dev"
```

to the require section of your `composer.json` file.

__After install package:__

1. Add scripts to **composer.json**:
    ```json
    ...
    "scripts": {
        "server": "vendor/bin/site-start.sh --env-file=docker/.env-local",
        "server-run": "vendor/bin/site-run.sh --env-file=docker/.env-local",
        "server-exec": "vendor/bin/site-exec.sh --env-file=docker/.env-local"
    }
    ...
    ```
    where **"docker/.env-local"** relative path to your local env config (will be created in next step).
2. Run: ```composer server init```. This will create a **docker** folder in your project root directory.
3. Change **root-path** in _docker/nginx/conf-dynamic.d/sample.conf_
4. Run server: ```composer server up ```
5. Check nginx container ip and add to hosts file:
    ```bash
    docker inspect sample_nginx
    ```
    view **"IPAddress"** and add to:
    ```bash
    sudo nano /etc/hosts
    ```
    _172.18.0.4 sample.io_ (for example)  
    save and check it.
6. Open browser and check **sample.io**
    
    
**LIFEHACKS** 
---

 - Add static network layer
    1. Create **docker-compose.local.yml** in your docker folder
    2. Paste:
        ```yml
        version: '2'
        
        services:
          nginx:
            networks:
              main_x:
                ipv4_address: 172.30.0.5
                
        networks:
          main_x:
            driver: bridge
            ipam:
              config:
                - subnet: 172.30.0.0/24
                  gateway: 172.30.0.1
        ```
    3. Change **SERVICES** variable in your local env (docker/.env-local) to:
        ```
        SERVICES="$SERVICES -f docker/docker-compose.common.yml -f docker/docker-compose.local.yml"
        ```
    4. Run: ```composer server restart``` and check it.