# Net zoals de single container production build wordt een multi phase setup
# gebruikt.
# Verschil hier is dat de nginx server wordt geserveerd op port 3000

FROM node:alpine as builder
WORKDIR /app
COPY ./package.json ./
RUN npm install
COPY . .
RUN npm run build

FROM nginx
EXPOSE 3000
COPY ./nginx/default.conf /etc/nginx/conf.d/default.conf
COPY --from=builder /app/build /usr/share/nginx/html
