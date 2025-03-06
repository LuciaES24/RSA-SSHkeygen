# SSH seguro

1. Generar par de claves SSH:

```
sudo ssh-keygen -t rsa -b 4096
```
Este comando genera la clave pública y la privada.

2. Crear el archivo Dockerfile:

```
FROM debian:latest

RUN apt-get update && apt-get install -y openssh-server
RUN mkdir /var/run/sshd
RUN echo "root:root" | chpasswd
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN sed -i 's/#PasswordAuthentication yes/PasswordAuthentication yes/' /etc/ssh/sshd_config

EXPOSE 22

CMD ["/usr/sbin/sshd", "-D"]
```

3. Contruir la imagen:

```
docker build -t ssh-debian .
```

4. Crear el archivo *docker-compose.yml*:

```
version: '3.8'

services:
  ssh.server:
    build: .
    container_name: ssh-container
    ports:
      - "2222:22"
    restart: always
    volumes:
      - /ssh_keys:/home/lucia/.ssh

volumes:
  ssh_keys:
```

5. Arrancar el docker:

```
docker compose up
```

6. Copiar la clave pública al contenedor:

```
docker cp ~/.ssh/id_rsa.pub <container_id>:/root/.ssh/authorized_keys
```

7. Acceder por ssh al contenedor con la clave privada para autenticarse.

```
ssh -p 2222 -i id_rsa root@localhost
```






