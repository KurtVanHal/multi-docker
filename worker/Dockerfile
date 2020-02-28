FROM node:alpine
WORKDIR /app
COPY ./package.json ./
RUN npm install
COPY . .
# Enkel het script wordt gewijzigd.
CMD ["npm", "run", "start"]