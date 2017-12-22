FROM nginx:1.12.2-alpine

RUN rm /etc/nginx/nginx.conf
COPY nginx.conf /etc/nginx/nginx.conf
COPY nginx_service /etc/init.d/nginx
RUN chmod +x /etc/init.d/nginx

COPY testsite /etc/nginx/sites-enabled/

EXPOSE 8080 