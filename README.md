Exercícios Docker: Um Tutorial Prático
Este tutorial tem como objetivo guiar você através de uma série de exercícios práticos com Docker, desde tarefas básicas até avançadas. Cada exercício é explicado passo a passo, com comandos e exemplos para facilitar o aprendizado.

🟢 Fácil
1. Rodando um container básico
Objetivo: Execute um container usando a imagem do Nginx e acesse a página padrão no navegador.

Exemplo de aplicação: Use a landing page do TailwindCSS como site estático dentro do container.

bash
Copy
# Baixar a imagem do Nginx
docker pull nginx

# Criar e rodar um container Nginx
docker run --name nome_container -d -p 8080:80 nginx

# Acessar o terminal do container
docker exec -ti nome_container bash
Explicação:

docker pull nginx: Baixa a imagem oficial do Nginx.

docker run --name nome_container -d -p 8080:80 nginx: Cria um container chamado nome_container, mapeia a porta 80 do container para a porta 8080 do host e roda em segundo plano (-d).

docker exec -ti nome_container bash: Acessa o terminal interativo do container.

2. Criando e rodando um container interativo
Objetivo: Inicie um container Ubuntu e interaja com o terminal dele.

Exemplo de aplicação: Teste um script Bash que imprime logs do sistema ou instala pacotes de forma interativa.

bash
Copy
# Criar e rodar um container Ubuntu com terminal interativo
docker run -dti --name nome_cont ubuntu

# Acessar o terminal do container
docker exec -ti nome_cont /bin/bash

# Dentro do container:
apt install nano 
cd /home/ubuntu
nano nome_bash.sh 
chmod +x nome_bash.sh (comando para permição de executavel para um arquivo, necessario sudo su para o acesso)
./nome_bash.sh (executar script)
Explicação:

docker run -dti --name nome_cont ubuntu: Cria um container Ubuntu com terminal interativo (-ti).

docker exec -ti nome_cont /bin/bash: Acessa o terminal do container.

apt install nano: Instala o editor de texto nano.

chmod +x nome_bash.sh: Torna o script executável.

3. Listando e removendo containers
Objetivo: Liste todos os containers em execução e parados, pare um container em execução e remova um container específico.

Exemplo de aplicação: Gerenciar containers de testes criados para verificar configurações ou dependências.

bash
Copy
# Listar containers em execução
docker ps

# Listar todos os containers (incluindo parados)
docker ps -a

# Parar um container
docker stop nome_container

# Remover um container
docker rm nome_container
Explicação:

docker ps: Lista containers em execução.

docker ps -a: Lista todos os containers, incluindo os parados.

docker stop nome_container: Para um container em execução.

docker rm nome_container: Remove um container específico.

4. Criando um Dockerfile para uma aplicação simples em Python
Objetivo: Crie um Dockerfile para uma aplicação Flask que retorna uma mensagem ao acessar um endpoint.

Exemplo de aplicação: Use a API de exemplo Flask Restful API Starter para criar um endpoint de teste.

Estrutura do Projeto:

Copy
flask-app/
├── app.py
├── requirements.txt
└── Dockerfile
app.py:

python
Copy
from flask import Flask

app = Flask(__name__)

@app.route('/')
def feira():
    return 'Não temos mais frutas'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
requirements.txt:

Copy
Flask==2.3.2
Dockerfile:

Dockerfile
Copy
FROM python:3.13  
WORKDIR /app  
COPY requirements.txt .  
RUN pip install --no-cache-dir -r requirements.txt  
COPY . .  
EXPOSE 5000  
CMD ["python", "app.py"]
Comandos:

bash
Copy
# Construir a imagem
docker build . -t nome-imagem

# Rodar o container
docker run -dti --name nome_container -p 5000:5000 nome_img
Explicação:

docker build . -t nome-imagem: Constrói a imagem Docker.

docker run -dti --name nome_container -p 5000:5000 nome_img: Roda o container mapeando a porta 5000.

🟡 Médio
5. Criando e utilizando volumes para persistência de dados
Objetivo: Execute um container MySQL e configure um volume para armazenar os dados do banco de forma persistente.

Exemplo de aplicação: Use o sistema de login e cadastro do Laravel Breeze, que usa MySQL.

bash
Copy
# Criar um volume
docker volume create volume_name

# Rodar um container MySQL com volume
docker run -dti --name vol1 -e MYSQL_ROOT_PASSWORD=senha -p 3306:3306 -v volume_name:/var/lib/mysql mysql:8.0.41-debian

# Acessar o MySQL
docker exec -ti vol1 /bin/bash
mysql -u root -p --protocol=tcp --port=3306
Explicação:

docker volume create volume_name: Cria um volume Docker.

docker run -dti --name vol1 ...: Roda um container MySQL com volume persistente.

docker exec -ti vol1 /bin/bash: Acessa o terminal do container.

mysql -u root -p --protocol=tcp --port=3306: Acessa o MySQL dentro do container.

6. Criando e rodando um container multi-stage
Objetivo: Utilize um multi-stage build para otimizar uma aplicação Go, reduzindo o tamanho da imagem final.

Exemplo de aplicação: Compile e rode a API do Go Fiber Example dentro do container.

Dockerfile:

Dockerfile
Copy
# Estágio 1: Compilação
FROM golang as exec
COPY app.go /go/src/app/
ENV GO111MODULE=auto
WORKDIR /go/src/app/
RUN go build -o app.go .

# Estágio 2: Imagem final
FROM alpine
WORKDIR /appexec
COPY --from=exec /go/src/app /appexec
RUN chmod -R 755 /appexec
ENTRYPOINT ./app.go
Comandos:

bash
Copy
# Construir a imagem
docker image build -t nome-imagem .

# Rodar o container
docker run -ti --name nome_app app-go:1.0 
Explicação:

O primeiro estágio compila o código Go.

O segundo estágio cria uma imagem leve usando Alpine e copia o binário compilado.

7. Construindo uma rede Docker para comunicação entre containers
Objetivo: Crie uma rede Docker personalizada e faça dois containers, um Node.js e um MongoDB, se comunicarem.

Exemplo de aplicação: Utilize o projeto MEAN Todos para criar um app de tarefas usando Node.js + MongoDB.

bash
Copy
# Criar uma rede Docker
docker network create rede1

# Rodar o container MongoDB
docker run -dti --name nome_container --network rede1 mongo

# Rodar o container Node.js
docker run -dti --name nome_container --network rede1 -p 3000:3000 node
Explicação:

docker network create rede1: Cria uma rede Docker.

docker run --network rede1: Conecta os containers à mesma rede.

8. Criando um compose file para rodar uma aplicação com banco de dados
Objetivo: Utilize Docker Compose para configurar uma aplicação Django com um banco de dados PostgreSQL.

Exemplo de aplicação: Use o projeto Django Polls App para criar uma pesquisa de opinião integrada ao banco.

docker-compose.yml:

yaml
Copy
version: '3.8'

services:
  db:
    image: postgres:14.17
    environment:
      POSTGRES_PASSWORD: "senha"
      POSTGRES_DB: "testedb"
    ports:
      - "5432:5432"
    volumes:
      - volpost:/var/lib/postgresql/data
    networks:
      - redepost

  django:
    image: django:onbuild
    ports:
      - "8080:8080"
    networks:
      - redepost

networks:
  redepost:
    driver: bridge

volumes:
  volpost:
Comandos:

bash
Copy
# Iniciar os containers
docker compose up -d
Explicação:

O arquivo docker-compose.yml define dois serviços: db (PostgreSQL) e django (Django).

docker compose up -d: Inicia os containers em segundo plano.

🔴 Difícil
9. Criando uma imagem personalizada com um servidor web e arquivos estáticos
Objetivo: Construa uma imagem baseada no Nginx ou Apache, adicionando um site HTML/CSS estático.

Exemplo de aplicação: Utilize a landing page do Creative Tim para criar uma página moderna hospedada no container.

Dockerfile:

Dockerfile
Copy
FROM nginx:stable-perl
COPY index.html /usr/share/nginx/html/
COPY styles.css /usr/share/nginx/html/
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
Comandos:

bash
Copy
# Construir a imagem
docker build -t nome_imagem .

# Rodar o container
docker run -dti -p 8080:80 --name nome_a_escolher nome_imagem
Explicação:

O Dockerfile copia os arquivos estáticos e a configuração do Nginx para a imagem.

docker run -dti -p 8080:80 ...: Roda o container mapeando a porta 80 para a 8080.