Certificado criado para mostrar o passo a passo de como aplicar o certificado SSL local em um container com **Docker**. 

### Crie um dockerfile com o código abaixo. 

```dockerfile
version: 'latest'
services:
  web:
    image:  httpd:latest
    container_name: meu-container
    ports:
      - "80:80"
      - "443:443"
```

#### Utilize o comando "$ docker compose up -d", em seguida verifique no local host seu container em aplicação. Note que não há certificado ainda. 

#### Em seguida adicione os volumes. 

```dockerfile
    volumes:
      - ./server.crt:/usr/local/apache2/conf/server.crt
      - ./server.key:/usr/local/apache2/conf/server.key
```

#### Agora crie os certificados locais com o comando "$ openssl req -x509 -newkey rsa:4096 -nodes -out cert.pem -keyout key.pem -days 365"

#### Após criar o certificado e a chave, você precisará aplicar o comando para tirar o comentário do comando ssl.conf e ativar os módulos ssl e socache. 

docker exec -it meu-container bash -c "sed -i -e 's/^#\(Include.*httpd-ssl.conf\)/\1/' -e 's/^#\(LoadModule .*mod_ssl.so\)/\1/' -e 's/^#\(LoadModule.*mod_socache_shmcb.so\)/\1/' conf/httpd.conf"

#### Certifique-se de que deu tudo certo com os comandos abaixo.

sudo docker exec -t meu-container bash -c "cat conf/httpd.conf | grep ssl.conf"

sudo docker exec -t meu-container bash -c "cat conf/httpd.conf | grep mod_ssl"

sudo docker exec -t meu-container bash -c "cat conf/httpd.conf | grep mod_socache_shmcb"

#### Caso o resultado apareça sem "#" basta acessar seu ip local e verificar o certificado.

