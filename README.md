## ELK com Docker

Iniciando com o `ELK`:

```
docker run -p 5601:5601 -p 9200:9200 -p 5044:5044 -p 5000:5000 -p 12201 -it -v /var/lib/elasticsearch --name elk sebp/elk
```

Também podemos utilizar o `docker-compose`:

`docker-compose.yml`:

```yml
elk:
  image: sebp/elk
  container_name: elk
  ports:
    - 5601
    - 9200
    - 5044
    - 5000
    - 12201
  volumes:
    - "/var/lib/elasticsearch"
```

Inicializando: `docker-compose up elk`

## Configuração inicial do ELK

```sh
# acessando o container
docker exec -it elk /bin/bash

# Configurando o input na porta 12201 e encaminhando para o elasticsearch no host 'localhost'
/opt/logstash/bin/logstash -e 'input { gelf { port => 12201 type => docker } } output { elasticsearch { hosts => ["localhost"] } }'
```

Podemos adicionar um arquivo no diretório: `/etc/logstash/conf.d/`, exemplo:

- Arquivo com configuração de input `logstash-gelf-input.conf`:

```
input {
    gelf {
        port => 12201
        type => docker
    }
}
```

- Arquivo com configuração de output `logstash-elasticsearch-output.conf`: 

```
output {
  elasticsearch { host => localhost }
}
```

## Enviando os logs do seu container para o Logstash via `gelf`

PS: A configuração necessária é `--log-driver=gelf --log-opt gelf-address=udp://<IP_CONTAINER_ELK>:<INPUT_PORT>`

```SH
docker run -it -p 5000:5000 --log-driver=gelf --log-opt gelf-address=udp://<IP_CONTAINER_ELK>:12201 --name helloworld netoht/docker-helloworld
```

# Referências

- [Documentação sebp/elk](http://elk-docker.readthedocs.org/)
- [Configuração de logging drivers](https://docs.docker.com/engine/reference/logging/overview/)
- [ELK and Docker 1.8](http://www.labouisse.com/how-to/2015/09/14/elk-and-docker-1-8/)
- [ELK-GELF](https://github.com/philippbauer/elk-gelf)
