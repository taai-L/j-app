FROM nginx
RUN rm -fR /usr/share/nginx/html
COPY /var/lib/jenkins/dokapa/git/j-app/html/index.html /usr/share/nginx/html
WORKDIR /usr/share/nginx/html
