FROM wordpress:php8.0-apache

# persistent dependencies
#RUN set -eux; \
#       apt-get update; \
#       apt-get install -y --no-install-recommends \
# XDEBUG
#               php-xdebug \
#       ; \
#       rm -rf /var/lib/apt/lists/*

RUN pecl install xdebug \
        && docker-php-ext-enable xdebug
        
COPY /print_env_xdebug_config_into_ini_file /etc/init.d/print_env_xdebug_config_into_ini_file
RUN chown root:root /etc/init.d/print_env_xdebug_config_into_ini_file \
		&& chmod u=rwx,go=rx /etc/init.d/print_env_xdebug_config_into_ini_file
ENTRYPOINT ["/etc/init.d/print_env_xdebug_config_into_ini_file"]
