Docker e Docker Compose: Simplificando o Desenvolvimento e o Deploy

Este resumo tem como objetivo explicar os conceitos fundamentais do Docker e do Docker Compose, abordando desde o isolamento de aplicações até a orquestração de múltiplos ambientes, destacando como essas ferramentas resolvem problemas reais no dia a dia do desenvolvimento de software.

O que é Docker?

O Docker é uma plataforma de código aberto baseada em tecnologia de containerização. Ele permite que desenvolvedores empacotem uma aplicação junto com todas as suas dependências, bibliotecas e configurações necessárias para que ela funcione.

Antes do Docker, um dos maiores dores de cabeça na área de tecnologia era o clássico problema do "na minha máquina funciona". Isso acontecia porque o ambiente de desenvolvimento (o computador do desenvolvedor) quase nunca era idêntico ao ambiente de produção (o servidor final). Diferenças nas versões do sistema operacional, do Node.js, do Python ou de bancos de dados frequentemente quebravam o sistema ao mudar de máquina.

O Docker resolve isso introduzindo o conceito de Container. Um container é um ambiente isolado, leve e seguro que roda diretamente sobre o kernel do sistema operacional hospedeiro. Ao contrário de uma Máquina Virtual (VM) tradicional, que precisa de um sistema operacional inteiro rodando dentro dela (o que consome muita memória e CPU), o container compartilha os recursos do sistema host. Isso garante que a aplicação rode exatamente da mesma forma em qualquer lugar: no seu notebook, no computador de um colega ou em um servidor na nuvem.

Imagem vs Container

Para entender o ecossistema Docker, é fundamental entender a diferença e a relação entre imagem e container:

Imagem: É um arquivo estático, imutável e de leitura. Pense na imagem como uma "receita de bolo" ou um "projeto de engenharia" (blueprint). Ela contém todas as instruções, código, runtime e dependências que a aplicação precisa para rodar, mas ela mesma não está "viva" ou executando.

Container: É a instância em execução de uma imagem. Usando a mesma analogia, o container é o "bolo pronto" ou a "casa construída" a partir do projeto. Você pode criar múltiplos containers idênticos a partir de uma única imagem. Enquanto a imagem é estática, o container tem um ciclo de vida: ele pode ser iniciado, pausado, parado e deletado.

O que é um Dockerfile?

O Dockerfile é o ponto de partida de tudo. Ele é um arquivo de texto simples, sem extensão, que contém uma sequência de comandos e instruções que o Docker lê para criar (dar o build) em uma imagem personalizada.

É no Dockerfile que você define o sistema base da sua aplicação, copia os arquivos do seu projeto para dentro do ambiente do Docker, instala as dependências necessárias e define o comando principal que deve ser executado quando o container for iniciado.

Exemplo Prático de Dockerfile (Node.js)

# Define a imagem base
FROM node:18-alpine

# Define o diretório de trabalho dentro do container
WORKDIR /app

# Copia os arquivos de dependências
COPY package*.json ./

# Instala as dependências do projeto
RUN npm install

# Copia o restante do código da aplicação
COPY . .

# Expõe a porta que a aplicação vai rodar
EXPOSE 3000

# Comando para iniciar a aplicação
CMD ["npm", "start"]


O que é Docker Compose?

Se o Docker é excelente para gerenciar um container isolado, o Docker Compose é a ferramenta ideal para coordenar múltiplos containers que precisam trabalhar juntos. Ele serve para orquestrar ambientes de desenvolvimento locais multiplataforma.

Quando usar e qual problema ele resolve?

Imagine que a sua aplicação precisa de uma API (Node.js), um banco de dados (PostgreSQL) e um serviço de cache (Redis). Gerenciar isso apenas com o Docker tradicional exigiria que você abrisse vários terminais, digitasse comandos longos para criar redes, volumes e configurar variáveis de ambiente manualmente para cada container.

O Docker Compose resolve essa complexidade centralizando a configuração de toda a arquitetura em um único arquivo de texto chamado docker-compose.yml. Com ele, você define todos os serviços, como eles se conectam entre si e suas configurações com uma sintaxe simples e organizada.

Exemplo Prático de docker-compose.yml

version: '3.8'

services:
  web:
    build: .
    ports:
      - "3000:3000"
    depends_on:
      - db
    environment:
      - DB_HOST=db

  db:
    image: postgres:15
    environment:
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
      POSTGRES_DB: meu_banco
    ports:
      - "5432:5432"


Exemplo Prático: O que acontece no docker compose up?

Quando abrimos o terminal na raiz do projeto (onde está o arquivo docker-compose.yml) e executamos o comando docker compose up, uma série de ações automatizadas acontece nos bastidores:

Leitura do Arquivo: O Docker Compose lê o arquivo docker-compose.yml para entender a estrutura e os serviços que precisam ser criados (no nosso exemplo, web e db).

Verificação de Imagens: Ele verifica se as imagens necessárias estão disponíveis localmente. No caso do db, se não houver a imagem do Postgres na máquina, ele faz o download (pull) automático do Docker Hub. No caso do web, ele olha para a instrução build: . e usa o Dockerfile local para construir a imagem da nossa aplicação.

Criação da Rede Virtual: O Compose cria automaticamente uma rede virtual isolada. Isso permite que o container da API (web) consiga conversar com o container do banco de dados (db) usando apenas o nome do serviço (ex: host: db), sem que precisemos descobrir IPs dinâmicos.

Criação e Inicialização dos Containers: Respeitando a ordem de dependências (graças ao depends_on), o Compose cria e inicia os containers. Ele garante que o banco de dados comece a subir antes da aplicação web.

Agregação de Logs: Por fim, ele anexa os outputs (logs) de todos os containers no seu terminal atual, permitindo que você veja em tempo real o banco iniciando e a API ficando online simultaneamente.

Referências

DOCKER. Docker Documentation. Disponível em: https://docs.docker.com/. Acesso em: jun. 2026.

DOCKER HUB. Official Repositories. Disponível em: https://hub.docker.com/. Acesso em: jun. 2026.

MERKEL, Dirk. Docker: Lightweight Linux Containers for Consistent Development and Deployment. Linux Journal, 2014.

SOUZA, Jeferson Fernando de. Descomplicando o Docker. 2. ed. São Paulo: Linuxtips, 2023.
