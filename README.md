# wordpress-docker-compose-xdebug3
A Docker compose to create images of WordPress using a dokerfile to create an image with Xdebug3.

This dockerfile use **wordpress:php8.0-apache** image to build a WordPress development image with Xdebug3.

I noticed that the environment variable XDEBUG_CONFIG:

```
A select set of settings can be set through an XDEBUG_CONFIG environment variable. In this situation, the xdebug. part should be dropped from the setting name.
```

is not working, so I made an ENTRYPOINT script that writes XDEBUG_CONFIG value inside a configuration file (**/usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini**) at container startup, before calling Apache ENTRYPOINT script **docker-entrypoint.sh apache2-foreground**.
So, instead of set:
```yaml
environment:
     XDEBUG_CONFIG: |
        mode=debug,develop
        client_host=host.docker.internal
        client_port=9003
        idekey="phpdebugkey"
```
it still uses xdebug. prefix
```yaml
environment:
      XDEBUG_CONFIG: |
        xdebug.mode=debug,develop
        xdebug.client_host=host.docker.internal
        xdebug.client_port=9003
        xdebug.idekey="phpdebugkey"
```

```host.docker.internal``` should work both on Windows and MacOS DockerDesktop, anyway it can be replaced with the the internal IP address used by the host.

## IDE Settings
IDE setting is quite easy and straight-forward,
for example in **NetBeans 12.3** put port *9003* and idekey *phpdebugkey* as stated inside the **docker-compose.yml**
<img width="897" alt="Schermata 2021-04-20 alle 17 12 55" src="https://user-images.githubusercontent.com/38914877/115421002-e5406d00-a1fb-11eb-96e9-ce5577f4e287.png">

If it still not works create a PHP file inside the container web directory with
```php
xdebug_info();
```
to check if Xdebug configuration is properly set as wished...
![Xdebug3](https://user-images.githubusercontent.com/38914877/115424477-09ea1400-a1ff-11eb-8732-34ebd34c6f6a.jpg)
If that does not works, check whether a firewall is active and if it is silently dropping or refusing connection to the 9003 port or if the port is not free for Xdebug.
