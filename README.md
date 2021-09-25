# wordpress-docker-compose-xdebug3
A Docker compose yml creating a WordPress stack with Xdebug3 for development purpose.

Somehow the default XDEBUG_CONFIG env variable does not work correctly:

https://xdebug.org/docs/all_settings
```
A select set of settings can be set through an XDEBUG_CONFIG environment variable. In this situation, the xdebug. part should be dropped from the setting name.
```

but not to fear, this image ENTRYPOINT writes XDEBUG_CONFIG env variable values inside a configuration file (**/usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini**) at container startup, before calling the original Apache ENTRYPOINT script **docker-entrypoint.sh apache2-foreground**.
So, instead of supporting only some settings dropping **xdebug.** from the setting name :
```html
A select set of settings can be set through an XDEBUG_CONFIG environment variable. In this situation, the xdebug. part should be dropped from the setting name.
```
```yaml
environment:
     XDEBUG_CONFIG: |
        mode=debug,develop
        client_host=host.docker.internal
        client_port=9003
        idekey="phpdebugkey"
```

You can pass all the settings as you write them on the configuration file, using the original settings name (still using xdebug. prefix )
```yaml
environment:
      XDEBUG_CONFIG: |
        xdebug.mode=debug,develop
        xdebug.client_host=host.docker.internal
        xdebug.client_port=9003
        xdebug.idekey="phpdebugkey"
```

```host.docker.internal``` should work both on Windows and MacOS DockerDesktop, anyway it can be replaced with the the internal IP address used by your host machine.

## IDE Settings
IDE setting is quite easy and straight-forward,
for example in **NetBeans 12.3** put port *9003* and idekey *phpdebugkey* as stated inside the **docker-compose.yml**
<img width="897" alt="Schermata 2021-04-20 alle 17 12 55" src="https://user-images.githubusercontent.com/38914877/115421002-e5406d00-a1fb-11eb-96e9-ce5577f4e287.png">

If it still not works create a PHP file inside the container web directory with
```php
xdebug_info();
```
to check if Xdebug configuration is set properly...
![Xdebug3](https://user-images.githubusercontent.com/38914877/115424477-09ea1400-a1ff-11eb-8732-34ebd34c6f6a.jpg)
If that does not works, check whether a firewall is active and if it is silently dropping or refusing connection to the 9003 port or if the port is not free for Xdebug.
