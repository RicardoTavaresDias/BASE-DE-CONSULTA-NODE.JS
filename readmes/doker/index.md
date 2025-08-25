- [VOLTAR](../../README.md)

</br>

<img src="./assets/logo.png" alt="Logo do projeto" width="200"/>

----

</br>

📌 `docker-compose.yml`

#### <span style="color:#efb423">*🔹 Postgres*</span>

````ts
npm i pg
````

````ts
services:
  db:
    image: postgres:latest
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: desafio
    ports:
      - "5432:5432"
````

#### <span style="color:#efb423">*🔹 Mysql*</span>

````ts
npm i mysql2
````

````ts
services:
  mysql:
    image: mysql:8.0
    container_name: APITasks
    restart: always
    ports:
      - '3306:3306'
    environment:
      - MYSQL_ROOT_PASSWORD=2478be40dd94892b1e2573d234d4529d
      - MYSQL_DATABASE=APITasks
````

---

### Volumes: criando banco temporário para testes

Criar um banco de dados de teste, para não usar o banco em produção ou desenvolvimento.

````ts
    volumes:
      - ./docker/setup.sql:/docker-entrypoint-initdb.d/init.sql 
````

Criar pasta na raiz do projeto docker/setup.sql

````ts
CREATE DATABASE desafio_test
````

----

#### <span style="color:#efb423">*🔹 API*</span>

````ts
version: "3.9"
services:
  api_chatbot_ai:
    build:
      context: .
      dockerfile: Dockerfile
    image: nodejs
    container_name: apiChatBot_AI

    # Adicionado para carregar variáveis de ambiente do seu arquivo .env
    env_file:
      - .env
    ports:
      - "3000:3000"
````

----

📌 `.dockerignore`

````
node_modules
.git
.env*
coverage
vitest.config.ts
requisicoes.http
README.md
````

📌 `Dockerfile`

````ts
# Define a imagem base como sendo a versão alpine do Node.js 22, que é leve e contém o ambiente Node.js necessário
# O 'AS builder' nomeia esta etapa para possível uso em multi-stage builds

FROM node:22-alpine AS builder

# Define o diretório de trabalho dentro do container onde os arquivos da aplicação serão copiados e executados

WORKDIR /app

# Copia todos os arquivos e diretórios do diretório local para o diretório de trabalho (/app) no container

COPY . ./

# Executa o comando npm ci com a flag --only=production, instalando apenas as dependências listadas em "dependencies" no package.json
# Isso evita a instalação de dependências de desenvolvimento, otimizando o tamanho da imagem

RUN npm ci --only=production

# Informa que a aplicação dentro do container escutará na porta 3333
# Isso é apenas uma documentação e não publica a porta automaticamente; é necessário mapear a porta ao rodar o container

EXPOSE 3333

# Define o comando padrão a ser executado quando o container for iniciado
# Executa o arquivo src/server.ts usando o Node.js

CMD [ "node", "src/server.ts" ]
````

🔹 Outro modelo Dockerfile

````ts
# Usa uma imagem base, por exemplo, o Node.js 18
FROM node:20-alpine3.20

# Define o diretório de trabalho dentro do contêiner
WORKDIR /usr/src/app

# Copia os arquivos de dependência e instala
COPY . .

# Executa o comando para instalação node_modules
RUN npm install

#Executa o comando para executar para converter o typescript
RUN npm run build

# Expõe a porta que a sua aplicação vai usar
EXPOSE 3000

# Comando para rodar a aplicação quando o contêiner iniciar
CMD ["npm", "start"]
````