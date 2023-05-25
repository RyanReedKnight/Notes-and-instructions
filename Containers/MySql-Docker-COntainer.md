
Command to set up mysql in a docker container locally.

docker run --name mysql-box \
-v $HOME/.mysql-storage:/var/lib/mysql \
-e MYSQL_ROOT_PASSWORD=password \
-e MYSQL_USER=knight \
-e MYSQL_PASSWORD=user-password \
-e MYSQL_DATABASE=php-store-a 
-p 3306:3306  \
-d mysql:lates
