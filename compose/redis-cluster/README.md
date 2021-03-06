<!-- TOC -->

- [Redis Cluster](#redis-cluster)
	- [Requisitos](#requisitos)
	- [Deploy Redis Cluster Utilizando 6 Nodes (full mode)](#deploy-redis-cluster-utilizando-6-nodes-full-mode)
	- [Deploy Redis Cluster Utilizando 3 Nodes (minimal mode)](#deploy-redis-cluster-utilizando-3-nodes-minimal-mode)
		- [Instalando o Redis no Node 1](#instalando-o-redis-no-node-1)
		- [Instalando o Redis no Node 2](#instalando-o-redis-no-node-2)
		- [Instalando o Redis no Node 3](#instalando-o-redis-no-node-3)
		- [Configurando o Redis Cluster](#configurando-o-redis-cluster)
		- [Comandos Úteis](#comandos-úteis)
	- [Backup](#backup)

<!-- TOC -->

# Redis Cluster

A documentação oficial do redis (https://redis.io/topics/cluster-tutorial) recomenda o setup com **6 nodes** dedicados.  Exemplo:

|node_1|node_2|node_3|node_4|node_5|node_6|
|-|-|-|-|-|-|
|redis-a1|redis-b1|redis-c1|redis-b2|redis-c2|redis-a2|

Neste cenário, os nodes do tipo **1** são **nodes master**. Os nodes do tipo **2** são **nodes slaves**.

Esse deployment também pode ser reduzido para **3 nodes**, sob pena de maior risco de perda de dados, para o seguinte cenário.

|node_1|node_2|node_3|
|-|-|-|
|groupA|groupB|groupC|
|redis-a1 + redis-c2|redis-b1 + redis-c2|redis-c1 + redis-b2|

Caso um dos hosts fique indisponível, o serviço ainda ficará disponível e capaz de se recuperar completamente após a recuperação do host.

## Requisitos

Acesse [essa página](../README.md), na seção **Requisitos**, para obter informações sobre como instalar as dependências para o funcionamento dos módulos.

## Deploy Redis Cluster Utilizando 6 Nodes (full mode)

//Em construção

## Deploy Redis Cluster Utilizando 3 Nodes (minimal mode)

### Instalando o Redis no Node 1

Copie o arquivo ``minimal/groupA/groupA.yaml`` para o host ``node_1``.

Execute os seguintes comandos para iniciar o **redis-master** e o **redis-replica** do grupo A.

```bash
sudo docker-compose -f groupA.yaml up -d
```

### Instalando o Redis no Node 2

Copie o arquivo ``minimal/groupB/groupB.yaml`` para o host ``node_2``.

Execute os seguintes comandos para iniciar o **redis-master** e o **redis-replica** do grupo B.

```bash
sudo docker-compose -f groupB.yaml up -d
```

### Instalando o Redis no Node 3

Copie o arquivo ``minimal/groupC/groupC.yaml`` para o host ``node_3``.

Execute os seguintes comandos para iniciar o **redis-master** e o **redis-replica** do grupo C.

```bash
sudo docker-compose -f groupC.yaml up -d
```

### Configurando o Redis Cluster

Execute o seguinte comando no host ``node_1`` (e confirme).

```bash
sudo docker run -i --rm --entrypoint redis-cli redis:5.0.3 --cluster create node_1:6379 node_2:6379 node_3:6379 node_1:6380 node_3:6380 node_2:6380 --cluster-replicas 1
```

### Comandos Úteis

Consulte os logs com o seguinte comando.

```bash
sudo docker-compose -f group{ID} logs -f
```

O termo ``{ID}`` deve ser substituído pelo número do node.

Pare o serviço com o seguinte comando.

```bash
sudo docker-compose -f group{ID} down
```

## Backup

O backup do Redis é salvo no diretório ``/data`` de cada host.
