FROM nginx
RUN rm -fR /usr/share/nginx/html
COPY /var/lib/jenkins/dokapa/git/html/ /usr/share/nginx/html
WORKDIR /usr/share/nginx/html
