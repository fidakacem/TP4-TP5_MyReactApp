1. Création des Dockerfiles

a/ Dockerfile du serveur:
from node:lts-alpine 
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 9000
ENV MONGO_URI=mongodb://mongodb:27017/mydb
CMD ["npm", "start"]

[Construction réalisée avec:"docker build -t mern-server ./server"]

b/ Dockerfile du client:

FROM node:lts-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
RUN npm install -g serve
EXPOSE 3000
CMD ["serve", "-s", "build", "-l", "3000", "--single"]

[Construction réalisée avec:"docker build -t mern-client ./client"]

2. Création d’un réseau Docker pour permettre la communication entre les conteneurs, Construction réalisée avec:
docker network create mern-network

3. Lancement  du TP
a/MongoDB: docker run -d --name mongodb --network mern-network mongo
b/Backend:docker run -d --name server --network mern-network -p 9000:9000 mern-server
c/Frontend:docker run -d --name client --network mern-network -p 3000:3000 mern-client

4. Création du fichier Docker Compose:
docker-compose.yml:

version: '3.8'

services:
  mongodb:
    image: mongo
    container_name: mongodb
    networks:
      - mern-network

  server:
    build: ./server
    container_name: server
    ports:
      - "9000:9000"
    depends_on:
      - mongodb
    environment:
      MONGO_URI: mongodb://mongodb:27017/mydb
    networks:
      - mern-network

  client:
    build: ./client
    container_name: client
    ports:
      - "3000:3000"
    depends_on:
      - server
    networks:
      - mern-network

networks:
  mern-network:
    driver: bridge

docker-compose.yml pour automatiser MongoDB,Le serveur(backend),Le client (frontend)
Lancement effectué avec: docker compose up --build

5. Vérification du fonctionnement:
       *Frontend :http://localhost:3000
       *Backend :http://localhost:9000/api/health
         ou réponse obtenue : {"message": "API is running"} 
