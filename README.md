# qloapps_booking-system

# Qloapps

## 1. Install docker

Command:
```bash
apt update

apt install docker

apt install docker-compose

## 2. Install Watchtower - For auto update

```bash
docker run --detach \
    --name watchtower \
    --volume /var/run/docker.sock:/var/run/docker.sock \
    containrrr/watchtower
