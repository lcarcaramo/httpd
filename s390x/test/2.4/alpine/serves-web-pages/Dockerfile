FROM quay.io/ibmz/httpd:2.4.46
COPY ./public-html/ /usr/local/apache2/htdocs/

# Test dependency
RUN apk add --no-cache curl
