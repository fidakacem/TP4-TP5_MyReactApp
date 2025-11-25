FROM node:lts-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 9000

ENV MONGO_URI=mongodb://mongodb:27017/mydb

CMD ["npm", "start"]
