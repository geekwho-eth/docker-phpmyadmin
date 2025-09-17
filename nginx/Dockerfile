FROM phpmyadmin:5.2.2-fpm-alpine

# install nginx supervisord
RUN apk add --no-cache nginx supervisor bash

# mkdir dir
RUN mkdir -p /run/nginx /var/log/supervisor

# config nginx
COPY nginx.conf /etc/nginx/nginx.conf
COPY phpmyadmin.conf /etc/nginx/conf.d/default.conf

# config supervisor
COPY supervisord.conf /etc/supervisord.conf

# Copy main script
COPY docker-entrypoint.sh /docker-entrypoint.sh

EXPOSE 80

USER root
ENTRYPOINT [ "/docker-entrypoint.sh" ]

CMD ["/usr/bin/supervisord", "-c", "/etc/supervisord.conf"]

