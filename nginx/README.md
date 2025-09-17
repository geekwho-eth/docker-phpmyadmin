## build images
docker build -t phpmyadmin:5.2.2-nginx .

## run container
docker run --name phpmyadmin -d -e PMA_HOST=dbhost phpmyadmin:5.2.2-nginx
