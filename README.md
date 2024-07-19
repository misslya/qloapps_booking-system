# qloapps_booking-system

# Qloapps

## 1. Install docker

Command:
```bash
apt update
apt install docker
apt install docker-compose
```

## 2. Install Watchtower  - For auto update

Command:
```bash
docker run --detach \
    --name watchtower \
    --volume /var/run/docker.sock:/var/run/docker.sock \
    containrrr/watchtower
```

## 3. Install Portainer - For docker management

Command:
```bash
docker volume create portainer_data

docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```

## 4. Install MariaDB / Mysql and PHPMyAdmin - For Database

Command:
```bash
docker network create phpmyadmin-tier

docker volume create --name mariadb_data

docker run -d --name mariadb -e ALLOW_EMPTY_PASSWORD=yes \
  --net phpmyadmin-tier \
  --volume mariadb_data:/bitnami/mariadb \
  bitnami/mariadb:latest
  
docker run -d --name phpmyadmin -p 8080:8080 -p 8443:8443 \
  --net phpmyadmin-tier \
  bitnami/phpmyadmin:latest
```

## 5. Install Qloapps

Command:
```bash
** add --net phpmyadmin-tier

docker run -tidp 80:80 -p 3306:3306 -p 2222:22 --name qloappsv160 -e USER_PASSWORD=qloappsuserpassword -e MYSQL_ROOT_PASSWORD=myrootpassword -e MYSQL_DATABASE=mydatabase —net phpmyadmin-tier webkul/qloapps_docker:latest

** after install 

docker exec -i qloappsv160 rm -rf /home/qloapps/www/hotelcommerce/install

** rename admin directory

docker exec -i qloappsv160 mv /home/qloapps/www/hotelcommerce/admin /home/qloapps/www/hotelcommerce/adminhtl


```

## 6. Traefik SSL (Secure Socket Layer) with Domain - Last Step

Traefik Installation First :
```bash
https://www.digitalocean.com/community/tutorials/how-to-use-traefik-v2-as-a-reverse-proxy-for-docker-containers-on-ubuntu-20-04
```
Then build a docker-compose:
```bash
version: '3'

services:
	qloapps:
		image: webkul/qloapps_docker:latest
		ports:
			- "2222:22"
			- "3306:3306"
			- "1000:80"
		environment:
			- USER_PASSWORD=aliadesu10
			- MYSQL_ROOT_PASSWORD=aliadesu10
			- MYSQL_DATABASE=qloapps
		networks:
			- phpmyadmin-tier
		labels:
			- traefik.enable=true
			- traefik.http.routers.qloapps.entrypoints=web,websecure
			- traefik.http.routers.qloapps.tls=true
			- traefik.http.routers.qloapps.tls.certresolver=lets-encrypt
			- traefik.http.services.qloapps.loadbalancer.server.port=80
			- traefik.http.routers.qloapps.rule=Host(`qloapps.scriptertechnology.com`)

networks:
  phpmyadmin-tier:
```
// Make sure network have the same with traefik and MySQL , then restart again in portainer
    - Open portainer, add existing web and phpmyadmin-tier
    - Restart container

Conclusion
    - Make sure domain is available
    - No port on that server
**** IF docker serve on port  :XX , that that to loadbalancer
**** IF host server on port XX: , put that on traefik.port

## 7. Automated backup with UNIX (Optional)   
    -use mysqldump command in bash and UNIX to create a corn job




