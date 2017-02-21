# Devops IFRN

Para a disciplina de sistemas coorporativos, foi definido que o grupo 2 iria cuidar da administração dos containers gitlab e jenkins na infraestrutura. As tarefas definidas foram:

- Definir estratégia de atualização do gitlab;
- Definir estratégia de atualização do jenkins;
- Criar uma rotina que possibilite implantar a imagem do container da aplicação nos servidores de aplicação;
- Criar o registry docker das imagens dos containers contendo os builds das aplicações.;

Para replicar o ambiente, começamos adquirindo 2 VPS para o trabalho. A hospedagem selecionada foi o Digital Ocean, pela facilidade de uso e por eles cobrarem por hora.

![virtual-machines](http://i.imgur.com/U6PGiON.png)

A máquina que abriga o container gitlab, necessita de pelo menos 2 cores e 2gb de ram, devido a enorme quantidade de tarefas no background que são processadas.

Já a que abriga o container do jenkins, pode ser um pouco mais modesta.

As configurações foram:

###### Gitlab
- 2GB RAM
- 2 vcores
- 30GB SSD
- Ubuntu 16.04
- Docker 1.13.0

###### Jenkins
- 1GB RAM
- 1 vcore
- 30GB SSD
- Ubuntu 16.04
- Docker 1.13.0

(A máquina do gitlab originalmente tinha 1GB de RAM e 1 vcore, porém ela foi redimensionada após verificar-mos que as especificações não dariam conta do serviço.)

As máquinas também já vieram com o docker 1.13.0 instalado por padrão.

### Instalação do Gitlab.

Para instalar o gitlab, usamos como referência a [página de documentação do gitlab para docker](https://docs.gitlab.com/omnibus/docker/README.html#where-is-the-data-stored).

Conectado a máquina por ```ssh```, executamos o seguinte comando:

```
sudo docker run --detach \
    --hostname gitlab.ivmelo.com \
    --publish 443:443 --publish 80:80 --publish 22:22 \
    --name gitlab \
    --restart always \
    --volume /srv/gitlab/config:/etc/gitlab \
    --volume /srv/gitlab/logs:/var/log/gitlab \
    --volume /srv/gitlab/data:/var/opt/gitlab \
    gitlab/gitlab-ce:latest
```

Em maiores detalhes:

- ```sudo docker run``` executa o container.
- ```--detach``` diz ao docker para liberar o terminal.
- ```--hostname gitlab.ivmelo.com``` diz qual endereço será usado externamente para acessar a aplicação. (Hostname).
- ```--publish 443:443 --publish 80:80 --publish 22:22``` associa (bind) as portas do container, as portas da máquina host.
- ```--restart always``` diz ao docker para reiniciar a máqiuna em caso de erro ou se ela travar.
- ```--volume``` monta os diretórios remotos na máquina host.
- ```gitlab/gitlab-ce:latest``` é a imagem docker que será baixada, caso não esteja presente no sistema.

Após isso, foi executado o comando ```docker logs gitlab --follow``` para acompanhar o resultado da instalação no terminal.

Feito isso, foi acessado a URL [http://gitlab.ivmelo.com](http://gitlab.ivmelo.com) para verificar o funcionamento do programa.
