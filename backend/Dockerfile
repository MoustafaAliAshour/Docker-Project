FROM node:18-alpine

WORKDIR /app

COPY package*.json ./

RUN npm install express mysql2 cors body-parser csv-writer

COPY server.js ./

RUN mkdir -p /app/data

EXPOSE 3000

CMD ["node", "server.js"]
