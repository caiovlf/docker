# Docker

Docker é uma ferramenta utilizada para criar um tipo de virtualização conhecida como contêineres. Hoje, podemos dizer que o docker está separado em três ferramentas:

 - docker-cli - executa os comandos desejados chamando a API REST
 - docker-engine - uma API REST que recebe os comandos e os passa para o containerd
 - containerd - aquele que de fato cria os contêineres

### Instalação

#### Debian

```
apt-get update
apt-get -y install apt-transport-https ca-certificates curl gnupg2 software-properties-common
curl -fsSL https://download.docker.com/linux/debian/gpg | apt-key add -
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/debian $(lsb_release -cs) stable"
apt-get update
apt-get install -y docker-ce docker-ce-cli containerd.io
usermod -G docker -a vagrant
```

Após a instalação, você pode executar o comando de teste para verificar se tudo está funcionando corretamente:

	docker run hello-world

Ao executar o comando **run** o Docker verifica se a imagem está disponível na máquina local, e caso não esteja ele faz o download no **registry** público padrão - um repositório de imagens - localizado em [hub.docker.com](hub.docker.com).

O comando **run** é a junção de dois outros comandos:

 - docker create
 - docker run
 
### Comandos de exemplo

```
docker pull alpine
docker run -d --name servidor -p 8080:80 nginx:alpine
docker run -ti debian
docker exec -ti ac10f32ed87e sh
docker top ac10f32ed87e
docker stats
```

Os contêineres quase sempre são efêmeros, e quando são removidos seus dados são descartados, a não ser que sejam persistidos em um volume. Este volume pode ser um diretório na própria máquina ou mesmo um serviço de NFS ou iSCSI. Também podemos "montar arquivos" diretamente dentro do container.
Para montar volumes dentro do contêiner utilizamos a flag **-v** ou **--mount**.

```
docker run -d --name apache -v /root/html:/usr/local/apache2/htdocs/ --publish 9090:80 httpd:end
docker volume create portainer_data # opcional
docker run -d -p 9000:9000 -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer
```

Também é possível passar variáveis de ambiente para dentro do contêiner, para facilitar alteração de endereços, senhas, portas e etc ou mesmo utilizá-las para a inicialização de um contêiner:

```
docker run -d -e MYSQL_ROOT_PASSWORD=4linux -e MYSQL_USER=hector -e MYSQL_PASSWORD=123 -e MYSQL_DATABASE=docker --name mysql mysql:5.7
```

Podemos fazer alterações dentro de um contêiner e salvá-lo como uma image:

```
docker run -ti --name gambiarra alpine
apk add vim curl telnet
# CTRL + P + Q
docker commit gambiarra gambiarra
```

Podemos salvar o contêiner como uma imagem, transferí-la para outro computador e carregá-la, sem precisar de acesso a internet ou a um registry local:

```
docker save gambiarra -o gambiarra.tar
docker load -i gambiarra.tar
```

# Dockerfile

O Dockerfile é o arquivo utilizado para criar imagens do docker. São muito simples, leves e fáceis de versionar.
