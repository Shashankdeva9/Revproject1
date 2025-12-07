FROM mysql:8.0

ENV MYSQL_DATABASE=revticketsnew
ENV MYSQL_ROOT_PASSWORD=root

COPY database_schema.sql /docker-entrypoint-initdb.d/

EXPOSE 3306
